-- Carregando a biblioteca para criar a interface
local Lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/7yhx/kwargs_Ui_Library/main/source.lua"))()

-- Criando a interface de usuário
local UI = Lib:Create{
    Theme = "Dark", -- Tema da interface
    Size = UDim2.new(0, 555, 0, 400) -- Tamanho padrão da UI
}

-- Criando a aba principal
local Main = UI:Tab{
    Name = "Main"
}

-- Adicionando um divisor na aba principal
local Divider = Main:Divider{
    Name = "Funções"
}

-- Função para pegar todos os itens/armas do mapa
local function GiveAllItems()
    -- Percorrer todos os objetos no workspace
    for _, item in pairs(workspace:GetChildren()) do
        -- Verificar se o item é uma ferramenta (arma ou item)
        if item:IsA("Tool") then
            -- Dar o item para o jogador
            item.Parent = game.Players.LocalPlayer.Backpack
        end
    end
    print("Todos os itens foram pegos.")
end

-- Botão para pegar todos os itens do mapa
local GetAllItems = Divider:Button{
    Name = "Pegar todos os itens/armas",
    Description = "Pega todos os itens/armas que estão no mapa.",
    Callback = function()
        GiveAllItems()
    end
}

-- Função para matar todos os jogadores (não modificado)
local KillAll = Divider:Button{
    Name = "Matar todos",
    Description = "Mata todos os jogadores no jogo!",
    Callback = function()
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                player.Character.Humanoid.Health = 0 -- Matar o jogador
            end
        end
        print("Todos os jogadores foram mortos.")
    end
}

-- Toggle para ativar/desativar o ESP
local ESPEnabled = false  -- Variável que controla o estado do ESP

local ToggleESP = Divider:Toggle{
    Name = "Ativar ESP",
    Description = "Ativa ou desativa o ESP para todos os jogadores.",
    Callback = function(State)
        ESPEnabled = State
        -- Atualiza os ESPs de todos os jogadores atuais no jogo
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character then
                if State then
                    CreateESP(player)  -- Cria o ESP se o Toggle estiver ativado
                else
                    RemoveESP(player)  -- Remove o ESP se o Toggle estiver desativado
                end
            end
        end
    end
}

-- Função para fechar a interface
local Quit = Main:Button{
    Name = "Fechar Interface",
    Description = "Fecha a interface de usuário.",
    Callback = function()
        UI:Quit{
            Message = "Saindo... Até logo!",
            Length = 1
        }
    end
}

-- Função para criar o ESP (azul) como já foi configurado anteriormente
local function CreateESP(player)
    local espPart = Instance.new("BillboardGui")
    espPart.Adornee = player.Character:WaitForChild("Head")
    espPart.Parent = player.Character
    espPart.Size = UDim2.new(0, 100, 0, 100)
    espPart.StudsOffset = Vector3.new(0, 2, 0)

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundColor3 = Color3.fromRGB(0, 0, 255) -- Cor azul
    frame.BorderSizePixel = 0
    frame.Parent = espPart

    ESPs[player] = espPart
end

-- Função para remover o ESP de um jogador
local function RemoveESP(player)
    if ESPs[player] then
        ESPs[player]:Destroy()
        ESPs[player] = nil
    end
end

-- Tabela para armazenar os ESPs criados para os jogadores
local ESPs = {}

-- Função para atualizar os ESPs quando um jogador entrar
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if ESPEnabled then
            CreateESP(player)
        end
    end)
end)

-- Função para remover ESP quando um jogador sair
game.Players.PlayerRemoving:Connect(function(player)
    RemoveESP(player)
end)
