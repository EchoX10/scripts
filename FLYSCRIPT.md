local IMAGE_URL = "https://s10.aconvert.com/convert/p3r68-cdx67/atpoz-cgaw4.png"
local AUDIO_URL = "https://s31.aconvert.com/convert/p3r68-cdx67/msjo7-3kg7t.mp3"

local FOLDER = "ScriptAssets"
local IMG_FILE = "img.png"
local AUDIO_FILE = "audio.mp3"

makefolder(FOLDER)
writefile(FOLDER .. "/" .. IMG_FILE, game:HttpGet(IMAGE_URL))
writefile(FOLDER .. "/" .. AUDIO_FILE, game:HttpGet(AUDIO_URL))

wait(1)

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- ====== 1. ESCURECER O MAPA ======
local lighting = game:GetService("Lighting")
lighting.Brightness = 0
lighting.ClockTime = 0
lighting.FogEnd = 100
lighting.FogStart = 0
lighting.Ambient = Color3.new(0, 0, 0)
lighting.OutdoorAmbient = Color3.new(0, 0, 0)

local colorCorrection = Instance.new("ColorCorrectionEffect")
colorCorrection.Parent = lighting
colorCorrection.Brightness = -0.8
colorCorrection.Contrast = 0.5
colorCorrection.TintColor = Color3.new(0.1, 0, 0.2)

local bloom = Instance.new("BloomEffect")
bloom.Parent = lighting
bloom.Intensity = 0.3
bloom.Size = 10

-- ====== 2. PERSONAGEM VOANDO ======
humanoid.PlatformStand = true
rootPart.Velocity = Vector3.new(0, 500, 0)
rootPart.CFrame = rootPart.CFrame * CFrame.Angles(math.rad(180), 0, 0)

spawn(function()
    while humanoid.Health > 0 do
        rootPart.Velocity = Vector3.new(math.random(-100, 100), 500, math.random(-100, 100))
        rootPart.CFrame = rootPart.CFrame * CFrame.Angles(math.rad(math.random(-20, 20)), math.rad(math.random(-20, 20)), math.rad(math.random(-20, 20)))
        wait(0.2)
    end
end)

-- ====== 3. EXPLODIR PARTES DO MAPA ======
local function explodeParts()
    for _, part in pairs(workspace:GetDescendants()) do
        if part:IsA("BasePart") and part ~= rootPart and part ~= character:FindFirstChild("Head") then
            spawn(function()
                local clone = part:Clone()
                clone.Parent = workspace
                clone.CFrame = part.CFrame
                clone.Anchored = false
                clone.CanCollide = false
                clone.Transparency = 0.5
                
                local velocity = Vector3.new(math.random(-300, 300), math.random(200, 600), math.random(-300, 300))
                clone.Velocity = velocity
                clone.RotVelocity = Vector3.new(math.random(-100, 100), math.random(-100, 100), math.random(-100, 100))
                
                spawn(function()
                    wait(0.5)
                    local explosion = Instance.new("Explosion")
                    explosion.Position = clone.Position
                    explosion.BlastRadius = 15
                    explosion.BlastPressure = 500
                    explosion.Parent = workspace
                end)
                
                wait(0.1)
                part.Transparency = 1
                part.CanCollide = false
                
                local fire = Instance.new("Fire")
                fire.Parent = clone
                fire.Size = 10
                
                wait(math.random(3, 8))
                clone:Destroy()
            end)
        end
    end
end

spawn(explodeParts)

-- ====== 4. TEXTURA DA IMAGEM EM TODO O MAPA E PERSONAGENS ======
local textureImage = getcustomasset(FOLDER .. "/" .. IMG_FILE)

local function applyTexture(obj)
    if obj:IsA("BasePart") and obj.Name ~= "HumanoidRootPart" then
        spawn(function()
            local decal = Instance.new("Decal")
            decal.Texture = textureImage
            decal.Face = Enum.NormalId.Front
            decal.Parent = obj
            
            local decal2 = decal:Clone()
            decal2.Face = Enum.NormalId.Back
            decal2.Parent = obj
            
            local decal3 = decal:Clone()
            decal3.Face = Enum.NormalId.Left
            decal3.Parent = obj
            
            local decal4 = decal:Clone()
            decal4.Face = Enum.NormalId.Right
            decal4.Parent = obj
            
            local decal5 = decal:Clone()
            decal5.Face = Enum.NormalId.Top
            decal5.Parent = obj
            
            local decal6 = decal:Clone()
            decal6.Face = Enum.NormalId.Bottom
            decal6.Parent = obj
            
            obj.Material = Enum.Material.Neon
            
            wait(0.5)
            local explosion = Instance.new("Explosion")
            explosion.Position = obj.Position
            explosion.BlastRadius = 10
            explosion.BlastPressure = 300
            explosion.Parent = workspace
        end)
    end
end

for _, obj in pairs(workspace:GetDescendants()) do
    applyTexture(obj)
end

workspace.DescendantAdded:Connect(applyTexture)

-- ====== 5. ÁUDIO E GUI TROLL (VERSÃO MELHORADA) ======
local playerGui = game:GetService("CoreGui")
local sound = Instance.new("Sound")
sound.SoundId = getcustomasset(FOLDER .. "/" .. AUDIO_FILE)
sound.Volume = 1
sound.Looped = true
sound.Parent = game:GetService("SoundService")
sound:Play()

-- Outro som de explosão tocando em loop
local sound2 = sound:Clone()
sound2.SoundId = "rbxassetid://2154316070" -- Som de explosão
sound2.Looped = true
sound2.Volume = 0.5
sound2.Parent = game:GetService("SoundService")
sound2:Play()

local images = {}

local function createImage()
    local sg = Instance.new("ScreenGui")
    sg.ResetOnSpawn = false
    sg.Parent = playerGui
    sg.IgnoreGuiInset = true
    sg.ZIndexBehavior = Enum.ZIndexBehavior.Global
    
    local img = Instance.new("ImageLabel")
    img.Size = UDim2.new(0, math.random(100, 300), 0, math.random(100, 300))
    img.Position = UDim2.new(math.random(), 0, math.random(), 0)
    img.BackgroundTransparency = 1
    img.Image = textureImage
    img.ZIndex = 999
    img.Rotation = math.random(-360, 360)
    img.Parent = sg
    
    table.insert(images, img)
    
    spawn(function()
        while img.Parent do
            img:TweenPosition(UDim2.new(math.random(), 0, math.random(), 0), "Out", "Sine", math.random(1, 3), true)
            img:TweenSizeAndPosition(
                UDim2.new(0, math.random(100, 500), 0, math.random(100, 500)),
                UDim2.new(math.random(), 0, math.random(), 0),
                "Out", "Sine", math.random(1, 3), true
            )
            wait(1.5 + math.random() * 2)
        end
    end)
    
    spawn(function()
        while img.Parent do
            img.Rotation = img.Rotation + 5
            wait(0.05)
        end
    end)
end

for i = 1, 99999 do
    createImage()
    wait(0.05)
end

spawn(function()
    while true do
        wait(0.3)
        if #images < 200 then
            for i = 1, 5 do
                createImage()
                wait(0.05)
            end
        end
    end
end)

-- ====== 6. TERREMOTO ======
spawn(function()
    while true do
        for _, part in pairs(workspace:GetDescendants()) do
            if part:IsA("BasePart") and part ~= rootPart then
                part.CFrame = part.CFrame + Vector3.new(math.random(-1, 1), 0, math.random(-1, 1))
            end
        end
        wait(0.1)
    end
end)

-- ====== 7. FAKE VÍRUS ======
spawn(function()
    wait(2)
    for i = 1, 50 do
        local gui = Instance.new("ScreenGui")
        gui.Parent = game:GetService("CoreGui")
        
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0, math.random(300, 500), 0, math.random(100, 150))
        frame.Position = UDim2.new(math.random(), 0, math.random(), 0)
        frame.BackgroundColor3 = Color3.new(1, 0, 0)
        frame.BackgroundTransparency = 0.3
        frame.Parent = gui
        
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.Text = "⚠️ VÍRUS DETECTADO ⚠️\n" .. math.random(100000, 999999) .. " arquivos infectados"
        label.TextColor3 = Color3.new(1, 1, 1)
        label.TextScaled = true
        label.Parent = frame
        
        wait(0.1)
    end
end)

print("☠️ TROLL ATIVADO COM SUCESSO ☠️")
