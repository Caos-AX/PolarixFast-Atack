local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local p = Players.LocalPlayer

-- CONFIG
local MultiplicadorDeCliques = 3
local ativado = false

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GuiMultiplicador"
screenGui.Parent = p:WaitForChild("PlayerGui")

-- BOTÃO PRINCIPAL (ON/OFF)
local botao = Instance.new("TextButton")
botao.Size = UDim2.new(0, 50, 0, 50)
botao.Position = UDim2.new(0, 20, 0.6, 0)
botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
botao.Text = "ON"
botao.TextColor3 = Color3.fromRGB(255, 255, 255)
botao.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(1, 0)
corner.Parent = botao

-- BOTÃO FECHAR (FIXO NO TOPO)
local fechar = Instance.new("TextButton")
fechar.Size = UDim2.new(0, 30, 0, 30)
fechar.Position = UDim2.new(1, -40, 0, 10) -- 👉 canto superior direito
fechar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
fechar.Text = "X"
fechar.TextScaled = true
fechar.TextColor3 = Color3.fromRGB(255, 255, 255)
fechar.Parent = screenGui

local fecharCorner = Instance.new("UICorner")
fecharCorner.CornerRadius = UDim.new(1, 0)
fecharCorner.Parent = fechar

-- TOGGLE
botao.MouseButton1Click:Connect(function()
    ativado = not ativado
    botao.Text = ativado and "ON" or "OFF"
end)

-- FECHAR GUI
fechar.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- DRAG (só no botão ON/OFF)
local dragging = false
local dragStart
local startPos

botao.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = botao.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        botao.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

-- LOOP (AUTO SOCO)
RunService.Heartbeat:Connect(function()
    if not ativado then return end

    local char = p.Character
    if not char then return end

    for _, tool in pairs(char:GetChildren()) do
        if tool:IsA("Tool") then
            for i = 1, MultiplicadorDeCliques do
                tool:Activate()

                local remote = tool:FindFirstChildOfClass("RemoteEvent")
                if remote then
                    remote:FireServer()
                end
            end
            tool.Enabled = true
        end
    end
end)
