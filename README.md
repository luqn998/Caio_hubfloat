
-- Checa GUI existente
if game.CoreGui:FindFirstChild("Caio_hub") then
    game.CoreGui.Caio_hub:Destroy()
end

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- Anti-morte supremo
if player.Character and player.Character:FindFirstChild("Humanoid") then
    local humanoid = player.Character.Humanoid
    humanoid.HealthChanged:Connect(function()
        if humanoid.Health < humanoid.MaxHealth then
            humanoid.Health = humanoid.MaxHealth
        end
    end)
end

-- GUI futurista arco-íris neon
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Caio_hub"
screenGui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,250,0,150)
frame.Position = UDim2.new(0.5,-125,0.5,-75)
frame.BackgroundColor3 = Color3.fromRGB(15,15,20)
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0,15)
corner.Parent = frame

local stroke = Instance.new("UIStroke")
stroke.Thickness = 3
stroke.Color = Color3.fromRGB(255,0,0)
stroke.Parent = frame

-- Título animado arco-íris
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,40)
title.Position = UDim2.new(0,0,0,0)
title.BackgroundTransparency = 1
title.Text = "Caio_hub - Boost"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

local hue = 0
-- Loop arco-íris para frame e título
RunService.RenderStepped:Connect(function()
    hue = (hue + 1) % 360
    local color = Color3.fromHSV(hue/360,1,1)
    stroke.Color = color
    title.TextColor3 = color
end)

-- Botão Boost 🚀
local boostButton = Instance.new("TextButton")
boostButton.Size = UDim2.new(0.9,0,0,60)
boostButton.Position = UDim2.new(0.05,0,0.4,0)
boostButton.BackgroundColor3 = Color3.fromRGB(0,0,255)
boostButton.Text = "Boost 🚀"
boostButton.TextColor3 = Color3.fromRGB(255,255,255)
boostButton.Font = Enum.Font.GothamBold
boostButton.TextScaled = true
boostButton.Parent = frame

local boostCorner = Instance.new("UICorner")
boostCorner.CornerRadius = UDim.new(0,12)
boostCorner.Parent = boostButton

-- Função do Boost 🚀 (pula 16 blocos)
boostButton.MouseButton1Click:Connect(function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = player.Character.HumanoidRootPart
        -- Velocidade vertical para pular ~16 blocos
        hrp.Velocity = Vector3.new(hrp.Velocity.X, 80, hrp.Velocity.Z)
    end
end)
