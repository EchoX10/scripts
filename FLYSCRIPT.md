local IMAGE_URL = "https://files.catbox.moe/hnlhfc.jpg"
local AUDIO_URL = "https://files.catbox.moe/grenxk.mp3"

local FOLDER = "ScriptAssets"
local IMG_FILE = "img.jpg"
local AUDIO_FILE = "audio.mp3"

makefolder(FOLDER)
writefile(FOLDER .. "/" .. IMG_FILE, game:HttpGet(IMAGE_URL))
writefile(FOLDER .. "/" .. AUDIO_FILE, game:HttpGet(AUDIO_URL))

wait(1)

local playerGui = game.Players.LocalPlayer.PlayerGui

local sound = Instance.new("Sound")
sound.SoundId = getcustomasset(FOLDER .. "/" .. AUDIO_FILE)
sound.Volume = 0.9
sound.Looped = true
sound.PlayOnRemove = false
sound.Parent = game:GetService("SoundService")
sound:Play()


local images = {}

local function createImage()
    local sg = Instance.new("ScreenGui")
    sg.ResetOnSpawn = false
    sg.Parent = playerGui

    local img = Instance.new("ImageLabel")
    img.Size = UDim2.new(0, 170, 0, 170)
    img.Position = UDim2.new(math.random(), 0, math.random(), 0)
    img.BackgroundTransparency = 1
    img.Image = getcustomasset(FOLDER .. "/" .. IMG_FILE)
    img.Parent = sg

    table.insert(images, img)

    spawn(function()
        while img.Parent do
            img:TweenPosition(UDim2.new(math.random(), 0, math.random(), 0), "Out", "Sine", 2, true)
            wait(1.5 + math.random() * 2)
        end
    end)
end

for i = 1, 99999 do
    createImage()
    wait(0.15)
end

spawn(function()
    while true do
        wait(0.8)
        if #images < 60 then
            createImage()
        end
    end
end)
