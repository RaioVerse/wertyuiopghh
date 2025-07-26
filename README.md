-- Painel Completo para Vox Seas (Seleção de NPC, Auto TP, Auto Click, Delay Customizável)
-- Feito para executores mobile/PC. Adaptar nome do RemoteEvent de ataque!

local Player = game.Players.LocalPlayer
local Char = Player.Character or Player.CharacterAdded:Wait()
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RemoteAttack = ReplicatedStorage:FindFirstChild("RemoteEventDeAtaque") -- Troque pelo nome real!

-- GUI SETUP
local gui = Instance.new("ScreenGui")
gui.Name = "PainelVoxSeas"
gui.Parent = game.CoreGui

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 260, 0, 380)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(35,35,45)
frame.BorderSizePixel = 0
frame.Parent = gui

local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, 0, 0, 36)
titulo.BackgroundTransparency = 1
titulo.Position = UDim2.new(0,0,0,0)
titulo.Text = "VOX SEAS TOOL"
titulo.Font = Enum.Font.SourceSansBold
titulo.TextSize = 22
titulo.TextColor3 = Color3.fromRGB(200,200,255)
titulo.Parent = frame

-- PAINEL DE NPCS
local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(0.6, -10, 0, 220)
scroll.Position = UDim2.new(0, 10, 0, 40)
scroll.CanvasSize = UDim2.new(0, 0, 0, 800)
scroll.BackgroundColor3 = Color3.fromRGB(25,25,35)
scroll.BorderSizePixel = 0
scroll.Parent = frame

local npcSelecionado
local function atualizarNPCs()
    for _,v in pairs(scroll:GetChildren()) do
        if v:IsA("TextButton") then v:Destroy() end
    end
    local y = 0
    for _, npc in ipairs(workspace:GetChildren()) do
        if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") and npc.Name ~= Player.Name and npc.Humanoid.Health > 0 then
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1,0,0,30)
            btn.Position = UDim2.new(0,0,0,y)
            btn.Text = npc.Name
            btn.Font = Enum.Font.SourceSansBold
            btn.TextColor3 = Color3.new(1,1,1)
            btn.BackgroundColor3 = (npcSelecionado == npc) and Color3.fromRGB(130,70,70) or Color3.fromRGB(40,40,60)
            btn.BorderSizePixel = 0
            btn.Parent = scroll
            btn.MouseButton1Click:Connect(function()
                npcSelecionado = npc
                atualizarNPCs()
            end)
            y = y + 30
        end
    end
    scroll.CanvasSize = UDim2.new(0,0,0,math.max(y,220))
end

spawn(function()
    while true do
        atualizarNPCs()
        wait(2)
    end
end)

-- Ativar/Desativar Auto TP
local autoTP = false
local btnTP = Instance.new("TextButton")
btnTP.Size = UDim2.new(0.38, 0, 0, 38)
btnTP.Position = UDim2.new(0.6, 10, 0, 40)
btnTP.Text = "Ativar Auto TP"
btnTP.Font = Enum.Font.SourceSansBold
btnTP.TextColor3 = Color3.new(1,1,1)
btnTP.BackgroundColor3 = Color3.fromRGB(60,130,60)
btnTP.BorderSizePixel = 0
btnTP.Parent = frame

btnTP.MouseButton1Click:Connect(function()
    autoTP = not autoTP
    btnTP.Text = autoTP and "Desativar Auto TP" or "Ativar Auto TP"
    btnTP.BackgroundColor3 = autoTP and Color3.fromRGB(160,60,60) or Color3.fromRGB(60,130,60)
end)

-- Ativar/Desativar Auto Click
local autoClick = false
local btnClick = Instance.new("TextButton")
btnClick.Size = UDim2.new(0.38, 0, 0, 38)
btnClick.Position = UDim2.new(0.6, 10, 0, 90)
btnClick.Text = "Ativar Auto Click"
btnClick.Font = Enum.Font.SourceSansBold
btnClick.TextColor3 = Color3.new(1,1,1)
btnClick.BackgroundColor3 = Color3.fromRGB(60,60,130)
btnClick.BorderSizePixel = 0
btnClick.Parent = frame

btnClick.MouseButton1Click:Connect(function()
    autoClick = not autoClick
    btnClick.Text = autoClick and "Desativar Auto Click" or "Ativar Auto Click"
    btnClick.BackgroundColor3 = autoClick and Color3.fromRGB(160,60,160) or Color3.fromRGB(60,60,130)
end)

-- Ajustar Delay do Auto Click
local labelDelay = Instance.new("TextLabel")
labelDelay.Size = UDim2.new(0.38, 0, 0, 20)
labelDelay.Position = UDim2.new(0.6, 10, 0, 140)
labelDelay.BackgroundTransparency = 1
labelDelay.Font = Enum.Font.SourceSans
labelDelay.TextSize = 15
labelDelay.Text = "Delay AutoClick (s):"
labelDelay.TextColor3 = Color3.fromRGB(200,200,200)
labelDelay.Parent = frame

local boxDelay = Instance.new("TextBox")
boxDelay.Size = UDim2.new(0.38, 0, 0, 26)
boxDelay.Position = UDim2.new(0.6, 10, 0, 160)
boxDelay.BackgroundColor3 = Color3.fromRGB(50,50,70)
boxDelay.Font = Enum.Font.SourceSans
boxDelay.Text = "0.001"
boxDelay.TextColor3 = Color3.new(1,1,1)
boxDelay.ClearTextOnFocus = false
boxDelay.Parent = frame

local delayClick = 0.001
boxDelay.FocusLost:Connect(function()
    local n = tonumber(boxDelay.Text)
    if n and n >= 0.001 then
        delayClick = n
    else
        boxDelay.Text = tostring(delayClick)
    end
end)

-- Loop de Auto TP
spawn(function()
    while true do
        if autoTP and npcSelecionado and npcSelecionado.Parent and npcSelecionado:FindFirstChild("HumanoidRootPart") and npcSelecionado:FindFirstChild("Humanoid") and npcSelecionado.Humanoid.Health > 0 then
            local hrp = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.CFrame = npcSelecionado.HumanoidRootPart.CFrame + Vector3.new(0,4,0)
            end
        end
        wait(0.2)
    end
end)

-- Loop de Auto Click
spawn(function()
    while true do
        if autoClick and npcSelecionado and npcSelecionado.Parent and npcSelecionado:FindFirstChild("Humanoid") and npcSelecionado.Humanoid.Health > 0 and RemoteAttack then
            -- Aqui você pode precisar ajustar os parâmetros do FireServer!
            pcall(function()
                RemoteAttack:FireServer(npcSelecionado)
            end)
        end
        wait(delayClick)
    end
end)

-- Fechar Painel (opcional)
local btnClose = Instance.new("TextButton")
btnClose.Size = UDim2.new(0, 28, 0, 28)
btnClose.Position = UDim2.new(1, -32, 0, 4)
btnClose.Text = "X"
btnClose.Font = Enum.Font.SourceSansBold
btnClose.TextSize = 20
btnClose.BackgroundColor3 = Color3.fromRGB(60,60,60)
btnClose.TextColor3 = Color3.new(1,1,1)
btnClose.BorderSizePixel = 0
btnClose.Parent = frame

btnClose.MouseButton1Click:Connect(function()
    gui:Destroy()
end)
