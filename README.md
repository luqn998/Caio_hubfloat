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

-- Variáveis plataforma
local platformPart
local platformActive = false
local targetY = 0
local hue = 0

-- GUI futurista arco-íris neon
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Caio_hub"
screenGui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,200,0,150)
frame.Position = UDim2.new(0.5,-100,0.5,-75)
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
title.Text = "Caio_hub - Plataforma"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

-- Botão Plataforma (Elevador)
local platformButton = Instance.new("TextButton")
platformButton.Size = UDim2.new(0.9,0,0,50)
platformButton.Position = UDim2.new(0.05,0,0.3,0)
platformButton.BackgroundColor3 = Color3.fromRGB(255,0,0)
platformButton.Text = "Plataforma OFF"
platformButton.TextColor3 = Color3.fromRGB(255,255,255)
platformButton.Font = Enum.Font.GothamBold
platformButton.TextScaled = true
platformButton.Parent = frame

local floatCorner = Instance.new("UICorner")
floatCorner.CornerRadius = UDim.new(0,12)
floatCorner.Parent = platformButton

-- Loop arco-íris para frame, título e botão
RunService.RenderStepped:Connect(function()
    hue = (hue + 1) % 360
    local color = Color3.fromHSV(hue/360,1,1)
    stroke.Color = color
    title.TextColor3 = color
    platformButton.BackgroundColor3 = color
end)

-- Função ativar/desativar plataforma
platformButton.MouseButton1Click:Connect(function()
    platformActive = not platformActive
    if platformActive then
        platformButton.Text = "Plataforma ON"
        if not platformPart then
            platformPart = Instance.new("Part")
            platformPart.Size = Vector3.new(20,1,20)
            platformPart.Anchored = true
            platformPart.CanCollide = true
            platformPart.Material = Enum.Material.Neon
            platformPart.BrickColor = BrickColor.new("Bright green")
            platformPart.Transparency = 0.6
            platformPart.Parent = Workspace
        end
    else
        platformButton.Text = "Plataforma OFF"
        if platformPart then
            platformPart:Destroy()
            platformPart = nil
        end
    end
end)

-- Atualiza posição da plataforma suavemente (Elevador)
RunService.RenderStepped:Connect(function()
    if platformActive and platformPart and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = player.Character.HumanoidRootPart
        targetY = hrp.Position.Y - 3
        local currentPos = platformPart.Position
        local newY = currentPos.Y + (targetY - currentPos.Y) * 0.2
        platformPart.Position = Vector3.new(hrp.Position.X, newY, hrp.Position.Z)
    end
end)
