-- Anti-Fling Ultra Light V3
-- Autor: cybernodry
-- Foco: Performance 0 Lag

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local active = false
local minimized = false

-- Criando a GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CyberNodryV3"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 200, 0, 130)
Main.Position = UDim2.new(0.5, -100, 0.4, 0)
Main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Main.BorderSizePixel = 0
Main.Active = true
Main.Parent = ScreenGui

local Corner = Instance.new("UICorner")
Corner.Parent = Main

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "cybernodry v3"
Title.TextColor3 = Color3.fromRGB(0, 255, 127)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Parent = Main

-- Botão principal
local Toggle = Instance.new("TextButton")
Toggle.Size = UDim2.new(0, 160, 0, 40)
Toggle.Position = UDim2.new(0.5, -80, 0.45, 0)
Toggle.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Toggle.Text = "Anti-Fling: OFF"
Toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
Toggle.Font = Enum.Font.Gotham
Toggle.Parent = Main

local TCorner = Instance.new("UICorner")
TCorner.Parent = Toggle

-- Botões de fechar e minimizar
local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 20, 0, 20)
Close.Position = UDim2.new(1, -25, 0, 5)
Close.Text = "X"
Close.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
Close.Parent = Main

local Min = Instance.new("TextButton")
Min.Size = UDim2.new(0, 20, 0, 20)
Min.Position = UDim2.new(1, -50, 0, 5)
Min.Text = "-"
Min.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
Min.Parent = Main

--- LÓGICA ANTI-LAG E ANTI-FLING ---

RunService.Stepped:Connect(function()
    if active then
        local char = LocalPlayer.Character
        if char then
            for _, part in ipairs(char:GetChildren()) do
                if part:IsA("BasePart") then
                    -- O segredo: desativa apenas o toque e zera a velocidade localmente
                    -- Isso impede que a física do hacker transfira força para você
                    part.CanTouch = false 
                    part.Velocity = Vector3.new(0, 0, 0)
                    part.RotVelocity = Vector3.new(0, 0, 0)
                end
            end
        end
    end
end)

--- FUNÇÕES DA UI ---

Toggle.MouseButton1Click:Connect(function()
    active = not active
    if active then
        Toggle.Text = "Anti-Fling: ON"
        Toggle.BackgroundColor3 = Color3.fromRGB(0, 150, 70)
    else
        Toggle.Text = "Anti-Fling: OFF"
        Toggle.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        -- Restaura o toque ao desligar
        local char = LocalPlayer.Character
        if char then
            for _, part in ipairs(char:GetChildren()) do
                if part:IsA("BasePart") then part.CanTouch = true end
            end
        end
    end
end)

Min.MouseButton1Click:Connect(function()
    minimized = not minimized
    Main:TweenSize(minimized and UDim2.new(0, 200, 0, 30) or UDim2.new(0, 200, 0, 130), "Out", "Quad", 0.2, true)
    Toggle.Visible = not minimized
end)

Close.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

-- Sistema de arrastar (Draggable) Otimizado
local dragging, dragInput, dragStart, startPos
Main.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
    end
end)
Main.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end
end)
UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
end)
