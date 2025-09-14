local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local SoundService = game:GetService("SoundService")

-- Remove efeitos antigos
for _, v in pairs(Lighting:GetChildren()) do
    if v:IsA("PostEffect") or v:IsA("Atmosphere") then v:Destroy() end
end

-- Correção de cor neutra
local cc = Instance.new("ColorCorrectionEffect", Lighting)
cc.TintColor = Color3.fromRGB(255, 255, 255)
cc.Saturation = 0.2
cc.Contrast = 0.15
cc.Brightness = 0.05

-- Bloom suave
local bloom = Instance.new("BloomEffect", Lighting)
bloom.Intensity = 1.2
bloom.Size = 30
bloom.Threshold = 2

-- Desfoque leve
local blur = Instance.new("BlurEffect", Lighting)
blur.Size = 1

-- Atmosfera natural
local atmosphere = Instance.new("Atmosphere", Lighting)
atmosphere.Density = 0.2
atmosphere.Offset = 0.1
atmosphere.Color = Color3.fromRGB(255, 255, 255) -- sem azul
atmosphere.Decay = Color3.fromRGB(120, 120, 120)
atmosphere.Glare = 0.3
atmosphere.Haze = 1

-- Luz ambiente e sombras
Lighting.Ambient = Color3.fromRGB(140, 140, 140)
Lighting.OutdoorAmbient = Color3.fromRGB(160, 160, 160)
Lighting.FogColor = Color3.fromRGB(200, 200, 200)
Lighting.FogEnd = 600
Lighting.FogStart = 0
Lighting.ClockTime = 13 -- meio-dia
Lighting.Brightness = 2
Lighting.GlobalShadows = true
Lighting.ShadowSoftness = 0.4

-- Chuva invisível (sem plataforma visível)
local rainPart = Instance.new("Part")
rainPart.Anchored = true
rainPart.CanCollide = false
rainPart.Transparency = 1 -- invisível
rainPart.Size = Vector3.new(500, 1, 500)
rainPart.Position = Vector3.new(0, 100, 0)
rainPart.Parent = Workspace

local rainEmitter = Instance.new("ParticleEmitter", rainPart)
rainEmitter.Texture = "rbxassetid://241837157"
rainEmitter.Rate = 400
rainEmitter.Lifetime = NumberRange.new(1, 2)
rainEmitter.Speed = NumberRange.new(20, 30)
rainEmitter.Size = NumberSequence.new(0.2)
rainEmitter.Transparency = NumberSequence.new(0.3)
rainEmitter.LockedToPart = true

local rainSound = Instance.new("Sound", SoundService)
rainSound.SoundId = "rbxassetid://9127857986"
rainSound.Looped = true
rainSound.Volume = 0.4
rainSound:Play()

-- Fogo e fumaça realistas
for _, part in pairs(Workspace:GetDescendants()) do
    if part:IsA("BasePart") and part.Name == "FireZone" then
        local fire = Instance.new("Fire", part)
        fire.Size = 8
        fire.Heat = 10
        fire.Color = Color3.fromRGB(255, 100, 50)
        fire.SecondaryColor = Color3.fromRGB(255, 200, 100)

        local smoke = Instance.new("Smoke", part)
        smoke.Color = Color3.fromRGB(80, 80, 80)
        smoke.Opacity = 0.4
        smoke.RiseVelocity = 6
    end
end

-- Água com reflexo e ondulação
for _, part in pairs(Workspace:GetDescendants()) do
    if part:IsA("BasePart") and part.Name == "WaterZone" then
        part.Material = Enum.Material.SmoothPlastic
        part.Reflectance = 0.4
        part.Transparency = 0.3
        local ripple = Instance.new("ParticleEmitter", part)
        ripple.Texture = "rbxassetid://243660364"
        ripple.Rate = 30
        ripple.Lifetime = NumberRange.new(1, 2)
        ripple.Speed = NumberRange.new(2, 4)
        ripple.Size = NumberSequence.new(0.5)
        ripple.Transparency = NumberSequence.new(0.5)
        ripple.VelocitySpread = 180
    end
end

print("Shader natural aplicado com sucesso!")
