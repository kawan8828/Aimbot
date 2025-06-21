campo de visão local = 40
local maxTransparency = 0.1 -- Transparência máxima dentro do círculo (0.1 = 10% de transparência)
local RunService = jogo:GetService("RunService")
Serviço de Entrada de Usuário local = jogo:GetService("Serviço de Entrada de Usuário")
Jogadores locais = jogo:GetService("Jogadores")
Câmera local = jogo.Espaço de trabalho.Câmera atual

FOVring local = Desenho.new("Círculo")
FOVring.Visible = verdadeiro
Espessura do anel FOV = 2
FOVring.Color = Color3.fromRGB(128, 0, 128) -- Cor roxa
FOVring.Filled = falso
AnelFOV.Raio = fov
FOVring.Position = Cam.ViewportSize / 2

função local updateDrawings()
    tamanho da porta de visualização da câmera local = Cam.ViewportSize
    FOVring.Position = Tamanho da janela de visualização da câmera / 2
fim

função local onKeyDown(entrada)
    se input.KeyCode == Enum.KeyCode.Delete então
        RunService:UnbindFromRenderStep("FOVUpdate")
        Anel FOV:Remover()
    fim
fim

UserInputService.InputBegan:Conectar(onKeyDown)

função local lookAt(alvo)
    lookVector local = (alvo - Cam.CFrame.Position).unidade
    local newCFrame = CFrame.new(Cam.CFrame.Position, Cam.CFrame.Position + lookVector)
    Cam.CFrame = novoCFrame
fim

função local calculateTransparency(distance)
    -- Ajuste a transparência com base na distância do centro do círculo
    local maxDistance = fov -- A distância máxima do centro do círculo
    transparência local = (1 - (distância / maxDistance)) * maxTransparency
    transparência de retorno
fim

função local getClosestPlayerInFOV(trg_part)
    local mais próximo = nulo
    local último = math.huge
    playerMousePos local = Cam.ViewportSize / 2

    para _, jogador em ipairs(Players:GetPlayers()) faça
        se jogador ~= Players.LocalPlayer então
            parte local = player.Character e player.Character:FindFirstChild(trg_part)
            se parte então
                ePos local, isVisible = Cam:WorldToViewportPoint(part.Position)
                distância local = (Vector2.new(ePos.x, ePos.y) - playerMousePos).Magnitude

                se distância < último e isVisible e distância < fov então
                    último = distância
                    mais próximo = jogador
                fim
            fim
        fim
    fim

    retornar mais próximo
fim

RunService.RenderStepped:Connect(função()
    atualizarDesenhos()
    local mais próximo = getClosestPlayerInFOV("Cabeça")
    se mais próximo e mais próximo.Character:FindFirstChild("Head") então
        lookAt(mais próximo.Personagem.Cabeça.Posição)
    fim
    
    se mais próximo então
        ePos local, isVisible = Cam:WorldToViewportPoint(mais próximo.Character.Head.Position)
        distância local = (Vector2.new(ePos.x, ePos.y) - (Cam.ViewportSize / 2)).Magnitude
        FOVring.Transparency = calcularTransparência(distância)
    outro
        FOVring.Transparency = 0.1 -- Mantenha completamente visível quando nenhum jogador estiver no FOV
    fim
fim)
