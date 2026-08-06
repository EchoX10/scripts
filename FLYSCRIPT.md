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

-- ====== 1. DIA E NOITE ALTERNADOS ======
local lighting = game:GetService("Lighting")

spawn(function()
    while true do
        -- DIA
        lighting.Brightness = 1
        lighting.ClockTime = 12
        lighting.FogEnd = 1000
        lighting.FogStart = 0
        lighting.Ambient = Color3.new(0.5, 0.5, 0.5)
        lighting.OutdoorAmbient = Color3.new(0.5, 0.5, 0.5)
        wait(0.5)
        
        -- NOITE
        lighting.Brightness = 0.3
        lighting.ClockTime = 0
        lighting.FogEnd = 50
        lighting.FogStart = 0
        lighting.Ambient = Color3.new(0.1, 0.1, 0.1)
        lighting.OutdoorAmbient = Color3.new(0.1, 0.1, 0.1)
        wait(0.5)
    end
end)

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

-- ====== 3. TEXTURA DA IMAGEM EM TODO O MAPA E PERSONAGENS ======
local textureImage = getcustomasset(FOLDER .. "/" .. IMG_FILE)

local function applyTexture(obj)
    if obj:IsA("BasePart") and obj.Name ~= "HumanoidRootPart" then
        spawn(function()
            -- Aplicar decal em todas as faces
            local faces = {
                Enum.NormalId.Front,
                Enum.NormalId.Back,
                Enum.NormalId.Left,
                Enum.NormalId.Right,
                Enum.NormalId.Top,
                Enum.NormalId.Bottom
            }
            
            for _, face in pairs(faces) do
                local decal = Instance.new("Decal")
                decal.Texture = textureImage
                decal.Face = face
                decal.Parent = obj
            end
            
            -- Deixar o objeto brilhante
            obj.Material = Enum.Material.Neon
            
            -- Mudar cor aleatoriamente
            spawn(function()
                while obj.Parent do
                    obj.Color = Color3.fromHSV(math.random(), 1, 1)
                    wait(0.5)
                end
            end)
        end)
    end
end

-- Aplicar em todos os objetos existentes
for _, obj in pairs(workspace:GetDescendants()) do
    applyTexture(obj)
end

-- Aplicar em novos objetos
workspace.DescendantAdded:Connect(applyTexture)

-- ====== 4. ÁUDIO E GUI TROLL ======
local playerGui = game:GetService("CoreGui")

-- Áudio principal
local sound = Instance.new("Sound")
sound.SoundId = getcustomasset(FOLDER .. "/" .. AUDIO_FILE)
sound.Volume = 1
sound.Looped = true
sound.Parent = game:GetService("SoundService")
sound:Play()

-- Áudio secundário (explosão em loop)
local sound2 = sound:Clone()
sound2.SoundId = "rbxassetid://2154316070"
sound2.Looped = true
sound2.Volume = 0.3
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
    local size = math.random(150, 400)
    img.Size = UDim2.new(0, size, 0, size)
    img.Position = UDim2.new(math.random(), 0, math.random(), 0)
    img.BackgroundTransparency = 1
    img.Image = textureImage
    img.ZIndex = 999
    img.Rotation = math.random(-360, 360)
    img.Parent = sg
    
    -- Adicionar efeito de brilho
    local bg = Instance.new("ImageLabel")
    bg.Size = UDim2.new(1, 20, 1, 20)
    bg.Position = UDim2.new(-0.05, 0, -0.05, 0)
    bg.BackgroundTransparency = 1
    bg.Image = "rbxassetid://10551959597" -- Efeito de brilho
    bg.ImageTransparency = 0.5
    bg.ZIndex = 998
    bg.Parent = img
    
    table.insert(images, img)
    
    -- Movimento e rotação
    spawn(function()
        while img.Parent do
            -- Movimento suave
            local targetPos = UDim2.new(math.random(), 0, math.random(), 0)
            img:TweenPosition(targetPos, "Out", "Sine", math.random(2, 4), true)
            
            -- Mudar tamanho
            local newSize = math.random(100, 500)
            img:TweenSize(UDim2.new(0, newSize, 0, newSize), "Out", "Sine", math.random(2, 4), true)
            
            wait(math.random(2, 5))
        end
    end)
    
    -- Rotação contínua
    spawn(function()
        while img.Parent do
            img.Rotation = img.Rotation + math.random(2, 10)
            wait(0.03)
            
            -- Mudar transparência
            img.ImageTransparency = math.sin(tick()) * 0.3 + 0.5
        end
    end)
end

-- Criar imagens em massa
for i = 1, 50 do
    createImage()
    wait(0.02)
end

-- Spawn contínuo de imagens
spawn(function()
    while true do
        wait(0.1)
        if #images < 300 then
            for i = 1, 3 do
                createImage()
                wait(0.02)
            end
        end
    end
end)

-- ====== 5. IMAGENS QUE SEGUEM O MOUSE ======
spawn(function()
    local mouse = player:GetMouse()
    while true do
        wait(0.5)
        if #images > 10 then
            for i = 1, 5 do
                local img = images[math.random(#images)]
                if img and img.Parent then
                    img:TweenPosition(
                        UDim2.new(0, mouse.X + math.random(-50, 50), 0, mouse.Y + math.random(-50, 50)),
                        "Out", "Sine", 0.5, true
                    )
                end
            end
        end
    end
end)

-- ====== 6. TREMOR NA TELA ======
spawn(function()
    while true do
        local camera = workspace.CurrentCamera
        local originalCF = camera.CFrame
        local shake = CFrame.new(
            math.random(-2, 2),
            math.random(-2, 2),
            math.random(-2, 2)
        )
        camera.CFrame = originalCF * shake
        wait(0.05)
        camera.CFrame = originalCF
        wait(0.1)
    end
end)

-- ====== 7. FAKE VÍRUS ======
spawn(function()
    wait(3)
    while true do
        wait(math.random(5, 15))
        local gui = Instance.new("ScreenGui")
        gui.Parent = game:GetService("CoreGui")
        gui.ZIndexBehavior = Enum.ZIndexBehavior.Global
        
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0, math.random(400, 700), 0, math.random(80, 150))
        frame.Position = UDim2.new(math.random(), 0, math.random(), 0)
        frame.BackgroundColor3 = Color3.new(1, 0, 0)
        frame.BackgroundTransparency = 0.2
        frame.ZIndex = 1000
        frame.Parent = gui
        
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.Text = "⚠️ ALERTA DE SEGURANÇA ⚠️\n" .. math.random(100000, 999999) .. " arquivos corrompidos"
        label.TextColor3 = Color3.new(1, 1, 1)
        label.TextScaled = true
        label.ZIndex = 1001
        label.Parent = frame
        
        local closeBtn = Instance.new("TextButton")
        closeBtn.Size = UDim2.new(0, 30, 0, 30)
        closeBtn.Position = UDim2.new(1, -35, 0, 5)
        closeBtn.BackgroundColor3 = Color3.new(1, 0, 0)
        closeBtn.Text = "X"
        closeBtn.TextColor3 = Color3.new(1, 1, 1)
        closeBtn.ZIndex = 1001
        closeBtn.Parent = frame
        closeBtn.MouseButton1Click:Connect(function()
            gui:Destroy()
            -- Criar mais popups quando fechar
            for i = 1, 5 do
                wait(0.1)
                local newGui = gui:Clone()
                newGui.Parent = game:GetService("CoreGui")
            end
        end)
        
        spawn(function()
            wait(0.5)
            frame:TweenPosition(UDim2.new(math.random(), 0, math.random(), 0), "Out", "Sine", 0.5, true)
        end)
    end
end)

-- ====== 8. TEXTO PISCANDO NO TOPO ======
spawn(function()
    local gui = Instance.new("ScreenGui")
    gui.Parent = game:GetService("CoreGui")
    gui.IgnoreGuiInset = true
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 0, 50)
    label.Position = UDim2.new(0, 0, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = "H̷̡̢̨͍̣͓͓̝̬̰̮͇̯͕͒̕͝Ä̸̠̖͖́̀̌̈̌͌̎̄̃̓͡H̵̢̢̙͓͔̤̱̠̞̟̼̹͑͊̓͑Ẫ̵̜̒̅͋H̶̼̾͊̐̇̎̃́͊̊̌̊͑͝͝͡A̶̧̛̛͓̘̳̩͚͓̞̳̹͚̥̮͎̿̋̍͐̑̀̒̏̈̑͗̽̊̒̿͌̅̕̚͜͜͜͝H̴̺̼̤̤̭̅̓͂̈́̊̌́̋̓̍̂̚͝͝Ä̶̛͍̯̙͓͔͖̳̃̀̀̓̀̊̌͘̕͠H̷̡̛̝͓̱͓̻̬̥̔̓̈́Ą̶̥̩̦̩̟̭̲͚͇̻͉̱̋̓͐͊̐̽̎̆͊̔̿̄̕̕͘͠H̸̫̤̘̝̟̤̺͚̠̝̬̼͇̗͍̦̩̤͋̉̿̎̔̾̈́̈̓̒̉̆̂̏͌͛̄͗̀̀̊̾̄̃͜͟͝ͅA̷̮̭̗͇͉͉͋̑̈́͐̎́̃͛̆̾̄̕H̶̡̧̱̬͎̞̳̲̞͚̥̞͎͕̤̞̱̦̻͙͍̖̄̑̓̍̊͋̂͌͐͟͟A̷̡̧̧͓̙̦̞̣̝̪̘̜̙͇̠̮̟̬͚̗͙̰͗̀̉͋͂̎͂̾̅̉̈́̉͛̓͊̀̀̊̄̎̈́̄͡͝͝"
    label.TextColor3 = Color3.new(1, 0, 0)
    label.TextScaled = true
    label.ZIndex = 9999
    label.Parent = gui
    
    while true do
        label.TextColor3 = Color3.new(1, 0, 0)
        label.Text = "H̷̡̢̨͍̣͓͓̝̬̰̮͇̯͕͒̕͝Ä̸̠̖͖́̀̌̈̌͌̎̄̃̓͡H̵̢̢̙͓͔̤̱̠̞̟̼̹͑͊̓͑Ẫ̵̜̒̅͋H̶̼̾͊̐̇̎̃́͊̊̌̊͑͝͝͡A̶̧̛̛͓̘̳̩͚͓̞̳̹͚̥̮͎̿̋̍͐̑̀̒̏̈̑͗̽̊̒̿͌̅̕̚͜͜͜͝H̴̺̼̤̤̭̅̓͂̈́̊̌́̋̓̍̂̚͝͝Ä̶̛͍̯̙͓͔͖̳̃̀̀̓̀̊̌͘̕͠H̷̡̛̝͓̱͓̻̬̥̔̓̈́Ą̶̥̩̦̩̟̭̲͚͇̻͉̱̋̓͐͊̐̽̎̆͊̔̿̄̕̕͘͠H̸̫̤̘̝̟̤̺͚̠̝̬̼͇̗͍̦̩̤͋̉̿̎̔̾̈́̈̓̒̉̆̂̏͌͛̄͗̀̀̊̾̄̃͜͟͝ͅA̷̮̭̗͇͉͉͋̑̈́͐̎́̃͛̆̾̄̕H̶̡̧̱̬͎̞̳̲̞͚̥̞͎͕̤̞̱̦̻͙͍̖̄̑̓̍̊͋̂͌͐͟͟A̷̡̧̧͓̙̦̞̣̝̪̘̜̙͇̠̮̟̬͚̗͙̰͗̀̉͋͂̎͂̾̅̉̈́̉͛̓͊̀̀̊̄̎̈́̄͡͝͝"
        wait(0.3)
        label.TextColor3 = Color3.new(1, 1, 1)
        label.Text = "Y̸̥̠̼̤͎͍͈̗͖̥͍̘̹̳̥͍͔̆͑̓͆́̋͜͠ͅO̷̧̡̨͚̤̗̠͎͕̯͉̻̝͈͕̘̙̰̠̤͖͖͓̾̉̓͊̒͂́͂́̋͒́͐̈́̋͋̌̽̋͠͠͠Ȗ̷͙̥̠̜̠̤̮̣̦̓̇̀̇̀̊̓̋͐͡͡ͅ B̸̨̧̼̤̹͔͚̭̞̗̖͖̖̮̹͚͚̲̻̤̘̪̾̆̐̀̾͛̽͆̾̿̀́͂̉̈́̈̋̆͋͘͟͝͝͝Ę̸̟̞̟͓̦̥̳̪̟͎̤̜̲͓̩͙̞͓̲̠̊͟͜͟ͅ H̶̬̹̳̠̖͈̖̿͋͌̄͂̀̾̌́͑̌̔́̚̕͝͠A̴̟̗̥͙̣̲̰̩̯͎̺̮̞̹͌̑̒̒̏̓̐͌̓̈́̄̄́̎̆̃͒̕͟͜͡C̷̛̛̖͚̳̼̟̹̞͔̫̽̿͆̿̍͂̔̅͗͠ͅK̷̢̭̙̺̫̤̖͎̙̟̱̟̋̓̿́̄́̚͜͝Ȩ̶̧̡̹̜͈̠̩̦̻̞̜͍̱̫̙͍̙̫̲͖̮̺̐̌͂̕Ḑ̶̨̛̖̟̹̦̰̹͍̣̯̝̪̻̟̘͎̞͈͇̏́̽̆͋̍̈́͛̆̑͟͟͝͝͝ X̸̢̡͕̪̻̰̟͈̣͕̻̪̯̳̄͟D̶̛̠̩̬̱̣̻̞̤̲̱̙͇̟͔͙̠̥̂̾̃̄̑̔̏͌͐̆̂̃̄́́̏̅̆̾̊̍͘͡͝ͅ"
        wait(0.3)
        label.TextColor3 = Color3.new(0, 1, 0)
        label.Text = "L̷̢̺̱̳̤͕̖͈̦̾͒̊́̅͗̇̔̋̆̂́́̈̎̓̽̈́̚͝͝͠͡Ǫ̵̨̰̻̱̺̦͉͔̰͌͊̈́̍̽̓̅̅̀̊͆̀͂̓́̚̚͝͝O̷̧̨̦͈͗̒̓̆̓̐́̒̓̕̚͝͠Ö̵̢̧̩̟̮̰̻͈̥͎̖̲̼̞̜̬̼̜̥̠̖͓̼̓̍̀̎̈́̄̈͑̏̎͗̍̋͊̈́̽͌̏̈́̈́̚͘͜͟͠L̸̛͔̱̩̊̈́̆̀́̊̈̀̎̈́̎̽̋̇̌͐̈́̀̚͡͝"
        wait(0.3)
    end
end)

print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
print("ㅤㅤㅤㅤㅤㅤㅤ")
