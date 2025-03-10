-- Verifica o PlaceId para garantir que estamos no jogo correto (Sea 2)
if game.PlaceId ~= 7449423635 then
    game:GetService("Players").LocalPlayer:Kick("Do not Support, Please wait...")
    return
end

-- Criar Interface UI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "AutoMoneyFarmUI"

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 300, 0, 200)
Frame.Position = UDim2.new(0.5, -150, 0.5, -100)
Frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Frame.BorderSizePixel = 2
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "Potato Hub - Auto Money Farm"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Title.Parent = Frame

-- Função para criar botões no menu
local function createButton(text, position, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(1, -10, 0, 40)
    Button.Position = UDim2.new(0, 5, 0, position)
    Button.Text = text
    Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.Parent = Frame
    Button.MouseButton1Click:Connect(callback)
end

-- Função para pegar a missão baseada no nível
local QuestData = {
    { Level = 1, MaxLevel = 9, Mon = "Bandit", Quest = "BanditQuest1", CFrameQuest = CFrame.new(1059, 15, 1550), CFrameMon = CFrame.new(1045, 27, 1560) },
    { Level = 10, MaxLevel = 14, Mon = "Monkey", Quest = "JungleQuest", CFrameQuest = CFrame.new(-1598, 35, 153), CFrameMon = CFrame.new(-1448, 67, 11) },
    { Level = 15, MaxLevel = 29, Mon = "Gorilla", Quest = "JungleQuest2", CFrameQuest = CFrame.new(-1220, 35, 345), CFrameMon = CFrame.new(-1130, 40, 350) }
}

-- Função para iniciar o farm de dinheiro
local function StartAutoMoneyFarm()
    local player = game:GetService("Players").LocalPlayer
    local MyLevel = player.Data.Level.Value
    local quest = nil
    
    -- Encontrar a missão baseada no nível do jogador
    for _, data in pairs(QuestData) do
        if MyLevel >= data.Level and MyLevel <= data.MaxLevel then
            quest = data
            break
        end
    end

    if quest then
        -- Vai até o ponto da missão
        player.Character.HumanoidRootPart.CFrame = quest.CFrameQuest
        wait(1)
        print("Missão iniciada: " .. quest.Quest)

        -- Começa a farmar, derrotando os NPCs repetidamente
        repeat
            wait(0.5)
            -- Atacar o NPC da missão
            player.Character.HumanoidRootPart.CFrame = quest.CFrameMon
            local args = {
                [1] = quest.Mon
            }
            game:GetService("ReplicatedStorage").Remotes:FindFirstChild("DamageEvent"):FireServer(unpack(args))
        until player.Data.Level.Value > MyLevel -- Para quando o nível aumentar
    else
        print("Nenhuma missão disponível para seu nível.")
    end
end

-- Criar botão para ativar o Auto Money Farm
createButton("Start Auto Money Farm", 50, function()
    StartAutoMoneyFarm()
end)

-- Fechar UI
createButton("Fechar", 100, function()
    ScreenGui:Destroy()
end)

print("Potato Hub - Auto Money Farm carregado com sucesso!")
