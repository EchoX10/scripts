-- BL5CKGUI COMPLETO v4
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local Debris = game:GetService("Debris")
local SoundService = game:GetService("SoundService")
local player = Players.LocalPlayer

-- Parent seguro
local guiParent
if pcall(function() return game:GetService("CoreGui") end) then
    guiParent = game:GetService("CoreGui")
else
    guiParent = player:WaitForChild("PlayerGui")
end

-- Remove GUI antiga
if guiParent:FindFirstChild("BL5CKGUI") then guiParent.BL5CKGUI:Destroy() end

-- Tween util
local function doTween(instance, props, time)
    time = time or 0.4
    TweenService:Create(instance, TweenInfo.new(time, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), props):Play()
end

-- ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BL5CKGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = guiParent

-- =========================
-- TELA DE CARREGAMENTO
-- =========================
local loadingFrame = Instance.new("Frame", ScreenGui)
loadingFrame.Size = UDim2.new(1,0,1,0)
loadingFrame.BackgroundColor3 = Color3.fromRGB(0,0,0)
loadingFrame.Position = UDim2.new(0,0,0,0)

local loadingText = Instance.new("TextLabel", loadingFrame)
loadingText.AnchorPoint = Vector2.new(0.5,0.5)
loadingText.Position = UDim2.new(0.5,0,0.5,0)
loadingText.Size = UDim2.new(0,300,0,60)
loadingText.BackgroundTransparency = 1
loadingText.Font = Enum.Font.GothamBold
loadingText.TextScaled = true
loadingText.TextColor3 = Color3.new(1,1,1)
loadingText.Text = "Carregando: 0%"

-- Main GUI (invisível no início)
local mainFrame = Instance.new("Frame", ScreenGui)
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0,360,0,520)
mainFrame.Position = UDim2.new(0.35,0,0.18,0)
mainFrame.BackgroundColor3 = Color3.fromRGB(18,18,18)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false
mainFrame.Active = true
mainFrame.Draggable = true
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0,12)
local stroke = Instance.new("UIStroke", mainFrame)
stroke.Color = Color3.fromRGB(90,90,90)
stroke.Thickness = 2

-- Title + close
local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1,-60,0,44)
title.Position = UDim2.new(0,16,0,6)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBlack
title.TextScaled = true
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Text = "BL5CKGUI"

local closeBtn = Instance.new("TextButton", mainFrame)
closeBtn.Size = UDim2.new(0,40,0,36)
closeBtn.Position = UDim2.new(1,-52,0,8)
closeBtn.BackgroundColor3 = Color3.fromRGB(130,10,10)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Text = "X"
closeBtn.TextScaled = true
closeBtn.TextColor3 = Color3.fromRGB(255,255,255)
Instance.new("UICorner", closeBtn).CornerRadius = UDim.new(0,8)

-- Scrolling area
local scroll = Instance.new("ScrollingFrame", mainFrame)
scroll.Size = UDim2.new(1,-28,1,-70)
scroll.Position = UDim2.new(0,14,0,60)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 8
scroll.CanvasSize = UDim2.new(0,0,0,0)

local listLayout = Instance.new("UIListLayout", scroll)
listLayout.Padding = UDim.new(0,10)
listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
listLayout.SortOrder = Enum.SortOrder.LayoutOrder
listLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    scroll.CanvasSize = UDim2.new(0,0,0,listLayout.AbsoluteContentSize.Y + 20)
end)

-- Floating button
local floatBtn = Instance.new("TextButton", ScreenGui)
floatBtn.Size = UDim2.new(0,140,0,44)
floatBtn.Position = UDim2.new(0.05,0,0.82,0)
floatBtn.BackgroundColor3 = Color3.fromRGB(20,20,20)
floatBtn.Text = "BL5CKGUI"
floatBtn.Font = Enum.Font.GothamBold
floatBtn.TextScaled = true
floatBtn.TextColor3 = Color3.new(1,1,1)
floatBtn.Visible = false
floatBtn.Active = true
floatBtn.Draggable = true
Instance.new("UICorner", floatBtn).CornerRadius = UDim.new(0,10)

closeBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    floatBtn.Visible = true
end)
floatBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = true
    floatBtn.Visible = false
end)

-- Funções util
local function createSection(text)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1,-20,0,32)
    lbl.BackgroundTransparency = 1
    lbl.Text = text
    lbl.Font = Enum.Font.GothamBold
    lbl.TextScaled = true
    lbl.TextColor3 = Color3.fromRGB(190,190,190)
    lbl.Parent = scroll
    return lbl
end

local function createButton(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1,-36,0,44)
    btn.BackgroundColor3 = Color3.fromRGB(36,36,36)
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.GothamBold
    btn.TextScaled = true
    btn.Text = text
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,10)
    btn.Parent = scroll
    btn.MouseButton1Click:Connect(function()
        doTween(btn, {BackgroundColor3 = Color3.fromRGB(75,75,75)}, 0.06)
        task.delay(0.12, function() doTween(btn, {BackgroundColor3 = Color3.fromRGB(36,36,36)}, 0.12) end)
        pcall(callback)
    end)
    return btn
end

-- Sounds
local music1 = Instance.new("Sound", SoundService)
music1.SoundId = "rbxassetid://1848354534"
music1.Looped = true
music1.Volume = 4
local music2 = Instance.new("Sound", SoundService)
music2.SoundId = "rbxassetid://1848354534"
music2.Looped = true
music2.Volume = 4

-- =========================
-- PODERES / BOTÕES
-- =========================
-- Local Player
createSection("🌟 Local Player")
createButton("⚡ Super Velocidade", function()
    local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = 120 end
end)
createButton("⬆️ Pulo Alto", function()
    local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.JumpPower = 150 end
end)
createButton("💪 Força Máxima", function()
    local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if hum then hum.MaxHealth = 200 hum.Health = 200 end
end)
createButton("👻 Modo Fantasma (5s)", function()
    if not player.Character then return end
    for _,part in pairs(player.Character:GetDescendants()) do
        if part:IsA("BasePart") then part.Transparency = 1 end
    end
    task.wait(5)
    for _,part in pairs(player.Character:GetDescendants()) do
        if part:IsA("BasePart") then part.Transparency = 0 end
    end
end)
createButton("🕹️ Teleporte Rápido", function()
    local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if root then root.CFrame = root.CFrame + Vector3.new(math.random(-60,60),0,math.random(-60,60)) end
end)

-- Efeitos
createSection("✨ Efeitos")
createButton("🌈 Super Efeito", function()
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
    for i=1,25 do
        local p = Instance.new("Part", workspace)
        p.Anchored = true
        p.CanCollide = false
        p.Size = Vector3.new(2,2,2)
        p.Material = Enum.Material.Neon
        p.Color = Color3.fromHSV(math.random(),1,1)
        p.CFrame = player.Character.HumanoidRootPart.CFrame * CFrame.new(math.random(-20,20),math.random(2,12),math.random(-20,20))
        Debris:AddItem(p, 0.8 + math.random()/2)
        task.wait(0.03)
    end
end)
createButton("🌑 Escuridão (3s)", function()
    local oldB = Lighting.Brightness
    Lighting.Brightness = 0
    task.wait(3)
    Lighting.Brightness = oldB
end)
createButton("🔥 Explosão Sombria", function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local exp = Instance.new("Explosion")
        exp.Position = player.Character.HumanoidRootPart.Position
        exp.BlastRadius = 8
        exp.BlastPressure = 0
        exp.Parent = workspace
    end
end)
createButton("💥 Aura Hacker", function()
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
    for i=1,12 do
        local b = Instance.new("Part", workspace)
        b.Shape = Enum.PartType.Ball
        b.Anchored = true
        b.CanCollide = false
        b.Material = Enum.Material.Neon
        b.Color = Color3.fromHSV(math.random(),1,1)
        b.Size = Vector3.new(1.5,1.5,1.5)
        b.CFrame = player.Character.HumanoidRootPart.CFrame * CFrame.new(math.random(-6,6),math.random(-4,4),math.random(-6,6))
        Debris:AddItem(b,1.2)
    end
end)
createButton("💨 Velocidade Turbo (curto)", function()
    local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        local old = hum.WalkSpeed
        hum.WalkSpeed = 300
        task.delay(4, function() if hum then hum.WalkSpeed = old end end)
    end
end)

-- Sons
createSection("🎵 Sons")
createButton("🎶 Música 1", function()
    if music1.IsPlaying then music1:Stop() else music1:Play() end
end)
createButton("🎶 Música 2", function()
    if music2.IsPlaying then music2:Stop() else music2:Play() end
end)
createButton("🔇 Silenciar Todas", function()
    music1:Stop(); music2:Stop()
end)
createButton("🔊 Volume Máximo", function()
    music1.Volume = 8; music2.Volume = 8
end)
createButton("🎧 Música Hacker Instant", function()
    local s = Instance.new("Sound", SoundService)
    s.SoundId = "rbxassetid://1848354534"
    s.Volume = 5
    s:Play()
    Debris:AddItem(s, 12)
end)

-- Extras
createSection("💀 Extras")
createButton("🕳️ Noclip (liga)", function()
    if not player.Character then return end
    for _,part in pairs(player.Character:GetDescendants()) do
        if part:IsA("BasePart") then part.CanCollide = false end
    end
end)
createButton("💡 Iluminação Total (temporária)", function()
    local old = Lighting.Brightness
    Lighting.Brightness = 6
    task.delay(6, function() Lighting.Brightness = old end)
end)
createButton("🔥 Modo Caos (chuva de blocos)", function()
    for i=1,28 do
        local p = Instance.new("Part", workspace)
        p.Size = Vector3.new(2,2,2)
        p.Anchored = true
        p.CanCollide = false
        p.Material = Enum.Material.Neon
        p.Color = Color3.fromHSV(math.random(),1,1)
        p.CFrame = CFrame.new(math.random(-80,80),80,math.random(-80,80))
        Debris:AddItem(p,5)
        task.wait(0.05)
    end
end)
createButton("⚡ Pulão Infinito (curto)", function()
    local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.JumpPower = 200
        task.delay(6, function() if hum then hum.JumpPower = 50 end end)
    end
end)
createButton("💀 Modo BL5CK (partículas)", function()
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
    for i=1,14 do
        local p = Instance.new("Part", workspace)
        p.Size = Vector3.new(3,3,3)
        p.Anchored = true
        p.CanCollide = false
        p.Material = Enum.Material.Neon
        p.Color = Color3.fromHSV(math.random(),1,1)
        p.CFrame = player.Character.HumanoidRootPart.CFrame * CFrame.new(math.random(-8,8),math.random(-5,5),math.random(-8,8))
        Debris:AddItem(p,2)
    end
end)

-- =========================
-- ANIMAÇÃO DO CARREGAMENTO
-- =========================
task.spawn(function()
    local progress = 0
    while progress < 100 do
        progress = math.min(progress + 1,100)
        loadingText.Text = "Carregando: "..progress.."%"
        task.wait(0.04)
    end
    doTween(loadingFrame, {Position = UDim2.new(0,0,1,0)}, 0.6)
    task.wait(0.6)
    loadingFrame.Visible = false
    mainFrame.Visible = true
end)

print("[BL5CKGUI] carregado com sucesso.")
