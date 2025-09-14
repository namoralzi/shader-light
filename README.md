local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "ShaderPanel"

-- Limpa efeitos antigos
for _, v in pairs(Lighting:GetChildren()) do
    if v:IsA("PostEffect") or v:IsA("Atmosphere") then v:Destroy() end
end

-- Efeitos visuais
local cc = Instance.new("ColorCorrectionEffect", Lighting)
cc.TintColor = Color3.fromRGB(255, 255, 255)
cc.Saturation = 0.2
cc.Contrast = 0.15
cc.Brightness = 0.05

local bloom = Instance.new("BloomEffect", Lighting)
bloom.Intensity = 1.2
bloom.Size = 30
bloom.Threshold = 2

local blur = Instance.new("BlurEffect", Lighting)
blur.Size = 2 -- leve desfoque

local atmosphere = Instance.new("Atmosphere", Lighting)
atmosphere.Density = 0.3
atmosphere.Offset = 0.2
atmosphere.Color = Color3.fromRGB(255, 255, 255)
atmosphere.Decay = Color3.fromRGB(120, 120, 120)
atmosphere.Glare = 0.4
atmosphere.Haze = 1.2

-- Painel RGB
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 100)
frame.Position = UDim2.new(0.5, -100, 0.9, -50)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.BorderSizePixel = 0

local rgb = Instance.new("UIGradient", frame)
rgb.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 0)),
    ColorSequenceKeypoint.new(0.33, Color3.fromRGB(0, 255, 0)),
    ColorSequenceKeypoint.new(0.66, Color3.fromRGB(0, 0, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 0))
}
rgb.Rotation = 0

-- Botões
local function createButton(name, text, pos)
    local btn = Instance.new("TextButton", frame)
    btn.Name = name
    btn.Size = UDim2.new(0, 60, 0, 30)
    btn.Position = UDim2.new(0, pos, 0, 35)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    btn.Text = text
    return btn
end

local btnDay = createButton("DayBtn", "Dia", 10)
local btnAfternoon = createButton("AfternoonBtn", "Tarde", 70)
local btnNight = createButton("NightBtn", "Noite", 130)

-- Funções de iluminação
local function setDay()
    Lighting.ClockTime = 9
    Lighting.Brightness = 2
    Lighting.FogEnd = 800
    Lighting.Ambient = Color3.fromRGB(200, 200, 200)
    Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    Lighting.FogColor = Color3.fromRGB(255, 255, 255)
end

local function setAfternoon()
    Lighting.ClockTime = 15
    Lighting.Brightness = 2
    Lighting.FogEnd = 600
    Lighting.Ambient = Color3.fromRGB(180, 160, 140)
    Lighting.OutdoorAmbient = Color3.fromRGB(220, 200, 180)
    Lighting.FogColor = Color3.fromRGB(240, 220, 200)
end

local function setNight()
    Lighting.ClockTime = 21
    Lighting.Brightness = 1
    Lighting.FogEnd = 300
    Lighting.Ambient = Color3.fromRGB(50, 50, 80)
    Lighting.OutdoorAmbient = Color3.fromRGB(80, 80, 120)
    Lighting.FogColor = Color3.fromRGB(30, 30, 50)
end

-- Conexões
btnDay.MouseButton1Click:Connect(setDay)
btnAfternoon.MouseButton1Click:Connect(setAfternoon)
btnNight.MouseButton1Click:Connect(setNight)

-- Inicializa com tarde
setAfternoon()