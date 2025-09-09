-- Checa se GUI já existe
if game.CoreGui:FindFirstChild("Caio_hub") then
    game.CoreGui.Caio_hub:Destroy()
end

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local PhysicsService = game:GetService("PhysicsService")

-- Variáveis
local floatActive = false
local floatDuration = 14
local floatSpeed = 39 -- 🚀 Velocidade atualizada
local floatStartTime = 0
local ragdolling = false

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

-- Função de ragdoll
local function setRagdoll(state)
    if not player.Character then return end
    local hum = player.Character:FindFirstChildOfClass("Humanoid")
    if not hum then return end

    if state and not ragdolling then
        ragdolling = true
        hum:ChangeState(Enum.HumanoidStateType.Physics)
        -- Adiciona giro aleatório
        if player.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = player.Character.HumanoidRootPart
            hrp.RotVelocity = Vector3.new(
                math.random(-20,20),
                math.random(-20,20),
                math.random(-20,20)
            )
        end
    elseif not state and ragdolling then
        ragdolling = false
        hum:ChangeState(Enum.HumanoidStateType.GettingUp)
    end
end

-- Detecta colisão
local function checkCollision(hrp)
    local ray = Ray.new(hrp.Position, hrp.Velocity.Unit * 3)
    local part, pos = workspace:FindPartOnRay(ray, player.Character, false, true)
    if part and part.CanCollide then
        setRagdoll(true)
        task.delay(2, function() -- ⏳ 2s depois levanta
            setRagdoll(false)
        end)
    end
end

-- Anti-Morte Supremo
local function antiMorte()
    player.CharacterAdded:Connect(function(char)
        local hum = char:WaitForChild("Humanoid")
        hum.Died:Connect(function()
            hum.Health = hum.MaxHealth
        end)
    end)
    if player.Character then
        local hum = player.Character:WaitForChild("Humanoid")
        hum.Died:Connect(function()
            hum.Health = hum.MaxHealth
        end)
    end
end
antiMorte()

-- Loop de movimento
RunService.Heartbeat:Connect(function(delta)
    if floatActive and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = player.Character.HumanoidRootPart
        local cam = workspace.CurrentCamera
        hrp.Velocity = cam.CFrame.LookVector * floatSpeed

        -- Detecta colisão e ativa ragdoll
        checkCollision(hrp)

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

-- Botão
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
