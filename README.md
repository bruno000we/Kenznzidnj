if game.PlaceId ~= 4442272183 then
    game:GetService("Players").LocalPlayer:Kick("Este script só funciona no Sea 2!")
    return
end

local player = game:GetService("Players").LocalPlayer
local rs = game:GetService("ReplicatedStorage")
local chestService = rs:WaitForChild("Remotes"):WaitForChild("ChestService")
local npcService = rs:WaitForChild("Remotes"):WaitForChild("DamageEvent")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "MoneyFruitFarmUI"

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 300, 0, 250)
Frame.Position = UDim2.new(0.5, -150, 0.5, -125)
Frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "Potato Hub - Money + Fruit (Sea 2)"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Title.Parent = Frame

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

-- Lista de Baús no Sea 2
local Chests = {
    CFrame.new(2200, 80, -6500),
    CFrame.new(2500, 75, -6600),
    CFrame.new(2300, 90, -6400)
}

-- NPCs para farmar
local NPCs = {
    { Mon = "Pirate Millionaire", CFrameMon = CFrame.new(2280, 77, -6500) },
    { Mon = "Pistol Billionaire", CFrameMon = CFrame.new(2425, 78, -6400) }
}

local autoChest, autoNPC, autoFruit = false, false, true

local function startChestFarm()
    autoChest = true
    while autoChest and autoFruit do
        for _, pos in pairs(Chests) do
            player.Character.HumanoidRootPart.CFrame = pos
            wait(0.5)
            chestService:FireServer()
        end
        wait(10) -- Espera para resetar os baús
    end
end

local function startNPCFarm()
    autoNPC = true
    while autoNPC and autoFruit do
        for _, data in pairs(NPCs) do
            player.Character.HumanoidRootPart.CFrame = data.CFrameMon
            wait(0.5)
            npcService:FireServer(data.Mon)
        end
    end
end

-- Função para detectar frutas no mapa
local function checkForFruit()
    while autoFruit do
        for _, obj in pairs(workspace:GetChildren()) do
            if obj:IsA("Tool") and obj:FindFirstChild("Handle") then
                print("Fruta detectada: " .. obj.Name)
                autoChest, autoNPC = false, false -- Para o farm
                
                -- Vai até a fruta
                player.Character.HumanoidRootPart.CFrame = obj.Handle.CFrame
                wait(1)
                
                -- Pega a fruta
                fireclickdetector(obj.Handle:FindFirstChildWhichIsA("ClickDetector"))
                wait(1)

                print("Fruta coletada! Agora está no seu inventário.")
                
                -- Volta a farmar
                autoChest, autoNPC = true, true
                startChestFarm()
                startNPCFarm()
            end
        end
        wait(5)
    end
end

createButton("Start Chest Farm", 50, function()
    startChestFarm()
end)

createButton("Start NPC Farm", 100, function()
    startNPCFarm()
end)

createButton("Ativar Auto Fruit", 150, function()
    checkForFruit()
end)

createButton("Fechar", 200, function()
    ScreenGui:Destroy()
    autoChest, autoNPC, autoFruit = false, false, false
end)

print("Potato Hub - Money + Auto Fruit (Sem Auto Guardar) carregado no Sea 2!")
