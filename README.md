-- Variáveis
local ESPEnabled = false

-- Função para criar um ESP em um jogador
local function CreateESP(player)
    if not player.Character then return end
    local char = player.Character
    local highlight = Instance.new("Highlight")
    highlight.Parent = char
    highlight.FillColor = Color3.new(1, 0, 0) -- Vermelho
    highlight.OutlineColor = Color3.new(1, 1, 1) -- Branco
    highlight.Enabled = true

    player.CharacterAdded:Connect(function(newChar)
        highlight:Destroy()
        CreateESP(player)
    end)

    player.AncestryChanged:Connect(function()
        if not player:IsDescendantOf(game) then
            highlight:Destroy()
        end
    end)
end

-- Função para ativar/desativar o ESP
local function ToggleESP(state)
    ESPEnabled = state
    if ESPEnabled then
        for _, player in ipairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer then
                CreateESP(player)
            end
        end
        game.Players.PlayerAdded:Connect(function(newPlayer)
            if ESPEnabled then
                CreateESP(newPlayer)
            end
        end)
    else
        for _, player in ipairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer and player.Character then
                for _, obj in ipairs(player.Character:GetChildren()) do
                    if obj:IsA("Highlight") then
                        obj:Destroy()
                    end
                end
            end
        end
    end
end

-- Configuração das teclas
local UserInputService = game:GetService("UserInputService")

UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Insert then
        ToggleESP(true)
    elseif input.KeyCode == Enum.KeyCode.Delete then
        ToggleESP(false)
    end
end)
