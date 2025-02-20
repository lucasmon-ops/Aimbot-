-- Script de ESP (Wallhack) + Aimbot para Roblox
-- CUIDADO: Usar scripts de trapaça pode resultar em banimento!

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = game.Workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Target = nil  -- Jogador alvo para o Aimbot
local AimbotEnabled = false -- Variável para ativar/desativar o Aimbot
local ESPEnabled = false -- Variável para ativar/desativar o ESP

-- Criar botões para Mobile
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local AimbotButton = Instance.new("TextButton", ScreenGui)
local ESPButton = Instance.new("TextButton", ScreenGui)

AimbotButton.Size = UDim2.new(0, 100, 0, 50)
AimbotButton.Position = UDim2.new(0.1, 0, 0.8, 0)
AimbotButton.Text = "Aimbot"
AimbotButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

ESPButton.Size = UDim2.new(0, 100, 0, 50)
ESPButton.Position = UDim2.new(0.3, 0, 0.8, 0)
ESPButton.Text = "ESP"
ESPButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)

AimbotButton.MouseButton1Click:Connect(function()
    AimbotEnabled = not AimbotEnabled
    AimbotButton.BackgroundColor3 = AimbotEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
end)

ESPButton.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    ESPButton.BackgroundColor3 = ESPEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(0, 0, 255)
end)

-- Função para criar ESP com linhas
function CreateESP(player)
    if player == LocalPlayer then return end -- Evita adicionar ESP ao próprio jogador
    
    local Highlight = Instance.new("Highlight")
    Highlight.Parent = player.Character
    Highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Vermelho
    Highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- Branco
    
    local Line = Instance.new("Beam")
    Line.Parent = player.Character
    Line.Attachment0 = Instance.new("Attachment", player.Character.Head)
    Line.Attachment1 = Instance.new("Attachment", LocalPlayer.Character.Head)
    Line.Color = ColorSequence.new(Color3.fromRGB(255, 255, 255)) -- Branco
    Line.Width0 = 0.1
    Line.Width1 = 0.1
end

-- Ativar ESP para todos os jogadores
for _, player in ipairs(Players:GetPlayers()) do
    player.CharacterAdded:Connect(function()
        wait(1) -- Pequeno delay para carregar o personagem
        CreateESP(player)
    end)
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        wait(1)
        CreateESP(player)
    end)
end)

-- Função do Aimbot (somente na cabeça dos inimigos)
function GetClosestEnemy()
    local closestPlayer = nil
    local shortestDistance = math.huge
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team and player.Character and player.Character:FindFirstChild("Head") then
            local headPosition = player.Character.Head.Position
            local screenPosition, onScreen = Camera:WorldToViewportPoint(headPosition)
            
            if onScreen then
                local mousePosition = UserInputService:GetMouseLocation()
                local distance = (Vector2.new(mousePosition.X, mousePosition.Y) - Vector2.new(screenPosition.X, screenPosition.Y)).Magnitude
                
                if distance < shortestDistance then
                    shortestDistance = distance
                    closestPlayer = player
                end
            end
        end
    end
    
    return closestPlayer
end

RunService.RenderStepped:Connect(function()
    if AimbotEnabled then
        Target = GetClosestEnemy()
        if Target and Target.Character and Target.Character:FindFirstChild("Head") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, Target.Character.Head.Position)
        end
    end
end)
