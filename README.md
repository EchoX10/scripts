local IMAGE_URL = "https://cdn.discordapp.com/attachments/1466640126485463244/1520918434408304723/image0.jpg?ex=6a42f12c&is=6a419fac&hm=af0e2c80352068083e46a78fb61b3c38e6ac117dd6a23df96f7ea5b37b706420&"
local AUDIO_URL = "https://files.catbox.moe/at6frb.mp3"

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
sound.Volume = 9
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

for i = 1, 60 do
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
