-- Checa se GUI já existe
if game.CoreGui:FindFirstChild("Caio_hub") then
    game.CoreGui.Caio_hub:Destroy()
end

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Variáveis
local floatActive = false
local floatDuration = 14
local floatSpeed = 39
local floatStartTime = 0
local lastUpdate = 0 -- Anti-lag, atualiza a cada 0.05s (~20FPS)

-- Criando GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Caio_hub"
screenGui.Parent = game.CoreGui
screenGui.ResetOnSpawn = false

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 180, 0, 100)
frame.Position = UDim2.new(0.5, -90, 0.5, -50)
frame.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = frame

local stroke = Instance.new("UIStroke")
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(120, 0, 0)
stroke.Parent = frame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0.3, 0)
title.BackgroundTransparency = 1
title.Text = "Caio_hub"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = frame

local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(1, -20, 0.4, -5)
floatButton.Position = UDim2.new(0, 10, 0.35, 0)
floatButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
floatButton.Text = "Float OFF"
floatButton.TextColor3 = Color3.fromRGB(255,255,255)
floatButton.Font = Enum.Font.GothamBold
floatButton.TextScaled = true
floatButton.Parent = frame

local cornerBtn = Instance.new("UICorner")
cornerBtn.CornerRadius = UDim.new(0, 8)
cornerBtn.Parent = floatButton

local timerLabel = Instance.new("TextLabel")
timerLabel.Size = UDim2.new(1, 0, 0.2, 0)
timerLabel.Position = UDim2.new(0, 0, 0.8, 0)
timerLabel.BackgroundTransparency = 1
timerLabel.Text = ""
timerLabel.TextColor3 = Color3.fromRGB(255,255,255)
timerLabel.Font = Enum.Font.GothamBold
timerLabel.TextScaled = true
timerLabel.Parent = frame

-- Loop de movimento com anti-lag
RunService.Heartbeat:Connect(function(delta)
    if floatActive and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        -- Limita atualizações para reduzir lag
        if tick() - lastUpdate < 0.05 then return end
        lastUpdate = tick()

        local hrp = player.Character.HumanoidRootPart
        local cam = workspace.CurrentCamera
        -- Movimento suavizado (anti-detect)
        hrp.Velocity = (cam.CFrame.LookVector * floatSpeed)
        
        -- Timer
        local elapsed = tick() - floatStartTime
        local remaining = math.max(0, math.floor(floatDuration - elapsed))
        timerLabel.Text = tostring(remaining).."s"

        if elapsed >= floatDuration then
            floatActive = false
            hrp.Velocity = Vector3.zero
            floatButton.Text = "Float OFF"
            floatButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            timerLabel.Text = ""
        end
    end
end)

-- Botão Float
floatButton.MouseButton1Click:Connect(function()
    if not floatActive then
        floatActive = true
        floatStartTime = tick()
        floatButton.Text = "Float ON"
        floatButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
    else
        floatActive = false
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.Velocity = Vector3.zero
        end
        floatButton.Text = "Float OFF"
        floatButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        timerLabel.Text = ""
    end
end)
