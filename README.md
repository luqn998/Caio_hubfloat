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
frame.Size = UDim2.new(0,250,0,200)
frame.Position = UDim2.new(0.5,-125,0.5,-100)
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
title.Text = "Caio_hub - Plataforma e Pulo"
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

-- Botão Plataforma (Elevador)
local platformButton = Instance.new("TextButton")
platformButton.Size = UDim2.new(0.9,0,0,50)
platformButton.Position = UDim2.new(0.05,0,0.25,0)
platformButton.BackgroundColor3 = Color3.fromRGB(255,0,0)
platformButton.Text = "Plataforma OFF"
platformButton.TextColor3 = Color3.fromRGB(255,255,255)
platformButton.Font = Enum.Font.GothamBold
platformButton.TextScaled = true
platformButton.Parent = frame

local floatCorner = Instance.new("UICorner")
floatCorner.CornerRadius = UDim.new(0,12)
floatCorner.Parent = platformButton

-- Botão Boost de pulo
local jumpButton = Instance.new("TextButton")
jumpButton.Size = UDim2.new(0.9,0,0,50)
jumpButton.Position = UDim2.new(0.05,0,0.6,0)
jumpButton.BackgroundColor3 = Color3.fromRGB(0,0,255)
jumpButton.Text = "Pular 15 blocos"
jumpButton.TextColor3 = Color3.fromRGB(255,255,255)
jumpButton.Font = Enum.Font.GothamBold
jumpButton.TextScaled = true
jumpButton.Parent = frame

local jumpCorner = Instance.new("UICorner")
jumpCorner.CornerRadius = UDim.new(0,12)
jumpCorner.Parent = jumpButton

-- Loop arco-íris para frame, título e botão plataforma
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
            platformPart.Transparency = 1 -- invisível
            platformPart.Parent = Workspace
        end

        -- Boost de 2x velocidade por 2 segundos
        local boostTime = 2
        local boostFactor = 2
        local originalSpeed = 0.2
        local boostedSpeed = originalSpeed * boostFactor
        local startTime = tick()

        local connection
        connection = RunService.RenderStepped:Connect(function()
            if not platformActive or not platformPart or not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
                connection:Disconnect()
                return
            end

            local hrp = player.Character.HumanoidRootPart
            targetY = hrp.Position.Y - 3
            local elapsed = tick() - startTime
            local speedFactor = (elapsed <= boostTime) and boostedSpeed or originalSpeed

            local currentPos = platformPart.Position
            local newY = currentPos.Y + (targetY - currentPos.Y) * speedFactor
            platformPart.Position = Vector3.new(hrp.Position.X, newY, hrp.Position.Z)
        end)

    else
        platformButton.Text = "Plataforma OFF"
        if platformPart then
            platformPart:Destroy()
            platformPart = nil
        end
    end
end)

-- Função pular automaticamente 15 blocos ao clicar
jumpButton.MouseButton1Click:Connect(function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = player.Character.HumanoidRootPart
        -- Ajusta a velocidade vertical para pular ~15 blocos
        hrp.Velocity = Vector3.new(hrp.Velocity.X, 75, hrp.Velocity.Z)
    end
end) GUI existente
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
