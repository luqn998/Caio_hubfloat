
-- Checa GUI existente
if game.CoreGui:FindFirstChild("Caio_hub") then
    game.CoreGui.Caio_hub:Destroy()
end

local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

local positions = {}
local moveSpeed = 37.11
local moving = false
local antiDeath = true

-- Anti-morte supremo
if player.Character and player.Character:FindFirstChild("Humanoid") then
    local humanoid = player.Character.Humanoid
    humanoid.HealthChanged:Connect(function()
        if antiDeath and humanoid.Health < humanoid.MaxHealth then
            humanoid.Health = humanoid.MaxHealth
        end
    end)
end

-- GUI futurista
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Caio_hub"
screenGui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,300,0,450)
frame.Position = UDim2.new(0.5,-150,0.5,-225)
frame.BackgroundColor3 = Color3.fromRGB(15,15,20)
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0,20)
corner.Parent = frame

local stroke = Instance.new("UIStroke")
stroke.Thickness = 3
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

-- Status
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1,-20,0,30)
statusLabel.Position = UDim2.new(0,10,1,-40)
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "Aguardando..."
statusLabel.TextColor3 = Color3.fromRGB(0,255,0)
statusLabel.Font = Enum.Font.GothamBold
statusLabel.TextScaled = true
statusLabel.Parent = frame

-- Botões de posição
local buttons, dots = {}, {}
local function criarBotaoPos(text, ordem)
    local btnFrame = Instance.new("Frame")
    btnFrame.Size = UDim2.new(1,-20,0,50)
    btnFrame.Position = UDim2.new(0,10,0,50 + (ordem-1)*60)
    btnFrame.BackgroundTransparency = 1
    btnFrame.Parent = frame

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.75,0,1,0)
    btn.Position = UDim2.new(0,0,0,0)
    btn.BackgroundColor3 = Color3.fromRGB(0,120,255)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.GothamBold
    btn.TextScaled = true
    btn.Parent = btnFrame

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0,12)
    btnCorner.Parent = btn

    local dot = Instance.new("Frame")
    dot.Size = UDim2.new(0,20,0,20)
    dot.Position = UDim2.new(0.8,0,0.1,0)
    dot.BackgroundColor3 = Color3.fromRGB(100,0,0)
    dot.Parent = btnFrame
    local dotCorner = Instance.new("UICorner")
    dotCorner.CornerRadius = UDim.new(1,0)
    dotCorner.Parent = dot

    local dotStroke = Instance.new("UIStroke")
    dotStroke.Thickness = 2
    dotStroke.Color = Color3.fromRGB(0,255,180)
    dotStroke.Parent = dot

    spawn(function()
        while true do
            if dot.BackgroundColor3 == Color3.fromRGB(0,255,0) then
                for i=1,10 do
                    dotStroke.Transparency = i/10
                    task.wait(0.05)
                end
                for i=10,1,-1 do
                    dotStroke.Transparency = i/10
                    task.wait(0.05)
                end
            else
                dotStroke.Transparency = 1
                task.wait(0.1)
            end
        end
    end)

    btn.MouseButton1Click:Connect(function()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            positions[ordem] = player.Character.HumanoidRootPart.Position
            dot.BackgroundColor3 = Color3.fromRGB(0,255,0)
            statusLabel.Text = "Posição "..ordem.." marcada!"
        end
    end)

    buttons[ordem] = btn
    dots[ordem] = dot
end

for i=1,5 do
    criarBotaoPos("Marcar Posição "..i,i)
end

-- Botão Teleguiado
local teleBtn = Instance.new("TextButton")
teleBtn.Size = UDim2.new(0.9,0,0,50)
teleBtn.Position = UDim2.new(0.05,0,0.7,0)
teleBtn.BackgroundColor3 = Color3.fromRGB(0,180,255)
teleBtn.Text = "Teleguiado"
teleBtn.TextColor3 = Color3.fromRGB(255,255,255)
teleBtn.Font = Enum.Font.GothamBold
teleBtn.TextScaled = true
teleBtn.Parent = frame

local teleCorner = Instance.new("UICorner")
teleCorner.CornerRadius = UDim.new(0,15)
teleCorner.Parent = teleBtn

-- Botão remover posições
local removeBtn = Instance.new("TextButton")
removeBtn.Size = UDim2.new(0.9,0,0,50)
removeBtn.Position = UDim2.new(0.05,0,0.82,0)
removeBtn.BackgroundColor3 = Color3.fromRGB(255,50,50)
removeBtn.Text = "Remover Posições"
removeBtn.TextColor3 = Color3.fromRGB(255,255,255)
removeBtn.Font = Enum.Font.GothamBold
removeBtn.TextScaled = true
removeBtn.Parent = frame

local removeCorner = Instance.new("UICorner")
removeCorner.CornerRadius = UDim.new(0,15)
removeCorner.Parent = removeBtn

removeBtn.MouseButton1Click:Connect(function()
    positions = {}
    for i=1,5 do
        dots[i].BackgroundColor3 = Color3.fromRGB(100,0,0)
    end
    statusLabel.Text = "Todas posições removidas!"
end)

-- Função teleguiado frame-a-frame, respeitando colisão, contornando paredes
local function teleguiado()
    if moving then return end
    moving = true
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then moving=false return end
    local hrp = char.HumanoidRootPart
    local humanoid = char:FindFirstChild("Humanoid")
    if not humanoid then moving=false return end

    for i=1,5 do
        if positions[i] then
            statusLabel.Text = "Indo para Posição "..i.."..."
            local target = positions[i]
            while (hrp.Position - target).Magnitude > 1 do
                local delta = RunService.RenderStepped:Wait()
                local dir = (target - hrp.Position).Unit

                -- Raycast para frente
                local ray = Ray.new(hrp.Position, dir*2)
                local hit, pos = Workspace:FindPartOnRayWithIgnoreList(ray, {char})
                local step = moveSpeed * delta

                if hit then
                    -- diminui passo para não atravessar a parede
                    step = step * 0.5
                end

                hrp.CFrame = hrp.CFrame + dir * step
            end
            statusLabel.Text = "Chegou Posição "..i
            task.wait(0.05)
        end
    end
    statusLabel.Text = "Teleguiado finalizado!"
    moving = false
end

teleBtn.MouseButton1Click:Connect(teleguiado)
