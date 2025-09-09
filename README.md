
-- Checa GUI existente
if game.CoreGui:FindFirstChild("Caio_hub") then
    game.CoreGui.Caio_hub:Destroy()
end

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

local floatActive = false
local floatSpeed = 37 -- velocidade em blocos por segundo
local floatDuration = 13 -- duração do float em segundos
local floatStartTime = 0
local lastUpdate = 0

-- Anti-morte supremo
if player.Character and player.Character:FindFirstChild("Humanoid") then
    local humanoid = player.Character.Humanoid
    humanoid.HealthChanged:Connect(function()
        if humanoid.Health < humanoid.MaxHealth then
            humanoid.Health = humanoid.MaxHealth
        end
    end)
end

-- GUI futurista
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
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(0,255,180)
stroke.Parent = frame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,40)
title.BackgroundTransparency = 1
title.Text = "Caio_hub"
title.TextColor3 = Color3.fromRGB(0,255,180)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

-- Botão Float
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0.9,0,0,50)
floatButton.Position = UDim2.new(0.05,0,0.3,0)
floatButton.BackgroundColor3 = Color3.fromRGB(0,120,255)
floatButton.Text = "Float OFF"
floatButton.TextColor3 = Color3.fromRGB(255,255,255)
floatButton.Font = Enum.Font.GothamBold
floatButton.TextScaled = true
floatButton.Parent = frame

local floatCorner = Instance.new("UICorner")
floatCorner.CornerRadius = UDim.new(0,12)
floatCorner.Parent = floatButton

-- Timer
local timerLabel = Instance.new("TextLabel")
timerLabel.Size = UDim2.new(1,0,0,30)
timerLabel.Position = UDim2.new(0,0,0.75,0)
timerLabel.BackgroundTransparency = 1
timerLabel.Text = ""
timerLabel.TextColor3 = Color3.fromRGB(0,255,0)
timerLabel.Font = Enum.Font.GothamBold
timerLabel.TextScaled = true
timerLabel.Parent = frame

-- Loop de movimento
RunService.Heartbeat:Connect(function(delta)
    if floatActive and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        if tick() - lastUpdate < 0.03 then return end -- atualiza ~30FPS
        lastUpdate = tick()

        local hrp = player.Character.HumanoidRootPart
        local cam = workspace.CurrentCamera

        -- Move na direção da câmera
        hrp.Velocity = cam.CFrame.LookVector * floatSpeed

        -- Timer
        local elapsed = tick() - floatStartTime
        local remaining = math.max(0, math.floor(floatDuration - elapsed))
        timerLabel.Text = tostring(remaining).."s"

        if elapsed >= floatDuration then
            floatActive = false
            hrp.Velocity = Vector3.zero
            floatButton.Text = "Float OFF"
            floatButton.BackgroundColor3 = Color3.fromRGB(0,120,255)
            timerLabel.Text = ""
        end
    end
end)

-- Função Float
floatButton.MouseButton1Click:Connect(function()
    if not floatActive then
        floatActive = true
        floatStartTime = tick()
        floatButton.Text = "Float ON"
        floatButton.BackgroundColor3 = Color3.fromRGB(0,255,0)
    else
        floatActive = false
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.Velocity = Vector3.zero
        end
        floatButton.Text = "Float OFF"
        floatButton.BackgroundColor3 = Color3.fromRGB(0,120,255)
        timerLabel.Text = ""
    end
end)
