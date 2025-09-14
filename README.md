local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local SoundService = game:GetService("SoundService")
local player = Players.LocalPlayer

-- Tela principal
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "ShaderLightUI"
gui.ResetOnSpawn = false

-- Painel principal
local panel = Instance.new("Frame", gui)
panel.Size = UDim2.new(0, 490, 0, 325)
panel.Position = UDim2.new(0.5, -245, 0.55, -162)
panel.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
panel.BorderSizePixel = 0
panel.ClipsDescendants = true
panel.Visible = false

-- Gradiente no painel
local grad = Instance.new("UIGradient", panel)
grad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 255, 200)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(130, 0, 255))
}
grad.Rotation = 60

-- Sombra
local shadow = Instance.new("ImageLabel", panel)
shadow.Size = UDim2.new(1, 48, 1, 48)
shadow.Position = UDim2.new(0, -24, 0, -24)
shadow.Image = "rbxassetid://1316045217"
shadow.ImageTransparency = 0.7
shadow.BackgroundTransparency = 1
shadow.ZIndex = 0

-- Cantos arredondados
local panelCorner = Instance.new("UICorner", panel)
panelCorner.CornerRadius = UDim.new(0, 18)

-- Cabeçalho com ícone
local header = Instance.new("Frame", panel)
header.Size = UDim2.new(1, 0, 0, 46)
header.BackgroundColor3 = Color3.fromRGB(18, 18, 19)
header.BorderSizePixel = 0
header.ClipsDescendants = true

local headerCorner = Instance.new("UICorner", header)
headerCorner.CornerRadius = UDim.new(0, 18)

local headerGrad = Instance.new("UIGradient", header)
headerGrad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 100, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(180, 0, 180))
}
headerGrad.Rotation = 0

local headerIcon = Instance.new("ImageLabel", header)
headerIcon.Size = UDim2.new(0, 36, 0, 36)
headerIcon.Position = UDim2.new(0, 10, 0.5, -18)
headerIcon.Image = "https://i.imgur.com/Lv8xCuA.jpeg"
headerIcon.BackgroundTransparency = 1

local headerTitle = Instance.new("TextLabel", header)
headerTitle.Size = UDim2.new(0, 300, 1, 0)
headerTitle.Position = UDim2.new(0, 58, 0, 0)
headerTitle.Text = "ShaderLight"
headerTitle.Font = Enum.Font.GothamBold
headerTitle.TextSize = 20
headerTitle.TextColor3 = Color3.fromRGB(255,255,255)
headerTitle.BackgroundTransparency = 1
headerTitle.TextXAlignment = Enum.TextXAlignment.Left

-- Botão de minimizar (resume)
local resumeBtn = Instance.new("TextButton", header)
resumeBtn.Size = UDim2.new(0, 36, 0, 36)
resumeBtn.Position = UDim2.new(1, -44, 0, 5)
resumeBtn.BackgroundTransparency = 1
resumeBtn.Text = "—"
resumeBtn.TextColor3 = Color3.fromRGB(230,230,230)
resumeBtn.Font = Enum.Font.GothamBlack
resumeBtn.TextSize = 30

-- Abas estilo Chrome
local tabsFrame = Instance.new("Frame", panel)
tabsFrame.Size = UDim2.new(1, -24, 0, 38)
tabsFrame.Position = UDim2.new(0, 12, 0, 52)
tabsFrame.BackgroundTransparency = 1

local tabButtons = {}
local tabContents = {}

local function createTab(tabName, iconId)
    local btn = Instance.new("TextButton", tabsFrame)
    btn.Size = UDim2.new(0, 120, 1, -7)
    btn.Position = UDim2.new(0, (#tabButtons)*128, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(48, 48, 60)
    btn.Text = "   "..tabName
    btn.TextColor3 = Color3.fromRGB(220,220,240)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    btn.AutoButtonColor = false
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 12)
    -- Icone na aba
    if iconId then
        local icon = Instance.new("ImageLabel", btn)
        icon.Size = UDim2.new(0, 22, 0, 22)
        icon.Position = UDim2.new(0, 6, 0.5, -11)
        icon.BackgroundTransparency = 1
        icon.Image = iconId
    end
    table.insert(tabButtons, btn)
    return btn
end

local function createTabContent()
    local cont = Instance.new("Frame", panel)
    cont.Size = UDim2.new(1, -24, 1, -100)
    cont.Position = UDim2.new(0, 12, 0, 92)
    cont.BackgroundTransparency = 1
    cont.Visible = false
    table.insert(tabContents, cont)
    return cont
end

-- Crie quantas abas quiser aqui!
local tabNames = {
    {name = "Visual", icon = "rbxassetid://7733960981"},
    {name = "Clima", icon = "rbxassetid://7734053498"},
    {name = "Som", icon = "rbxassetid://7733989023"},
    {name = "Partículas", icon = "rbxassetid://7734028463"},
}
for i,data in ipairs(tabNames) do
    createTab(data.name, data.icon)
    createTabContent()
end

-- Botão de adicionar nova aba (estilo Chrome)
local addTabBtn = Instance.new("TextButton", tabsFrame)
addTabBtn.Size = UDim2.new(0, 38, 0, 31)
addTabBtn.Position = UDim2.new(0, #tabNames*128 + 8, 0, 2)
addTabBtn.BackgroundColor3 = Color3.fromRGB(50,50,65)
addTabBtn.Text = "+"
addTabBtn.TextColor3 = Color3.fromRGB(170,230,255)
addTabBtn.Font = Enum.Font.GothamBlack
addTabBtn.TextSize = 26
addTabBtn.AutoButtonColor = true
Instance.new("UICorner", addTabBtn).CornerRadius = UDim.new(0, 12)

addTabBtn.MouseButton1Click:Connect(function()
    -- Exemplo: adiciona aba nova
    local idx = #tabButtons+1
    local btn = createTab("Nova "..idx, nil)
    btn.Position = UDim2.new(0, (idx-1)*128, 0, 0)
    local cont = createTabContent()
    cont.Visible = false
    btn.MouseButton1Click:Connect(function()
        for i,b in ipairs(tabButtons) do
            b.BackgroundColor3 = Color3.fromRGB(48,48,60)
            tabContents[i].Visible = false
        end
        btn.BackgroundColor3 = Color3.fromRGB(65,80,180)
        cont.Visible = true
    end)
end)

-- Troca de abas
for i,btn in ipairs(tabButtons) do
    btn.MouseButton1Click:Connect(function()
        for j,b in ipairs(tabButtons) do
            b.BackgroundColor3 = Color3.fromRGB(48,48,60)
            tabContents[j].Visible = false
        end
        btn.BackgroundColor3 = Color3.fromRGB(65,80,180)
        tabContents[i].Visible = true
    end)
end
-- Seleciona a primeira aba por padrão
tabButtons[1].BackgroundColor3 = Color3.fromRGB(65,80,180)
tabContents[1].Visible = true

-------------------- Conteúdo das abas (exemplo) --------------------
-- Visual
do
    local cont = tabContents[1]
    local btn = Instance.new("TextButton", cont)
    btn.Size = UDim2.new(0, 180, 0, 36)
    btn.Position = UDim2.new(0, 18, 0, 12)
    btn.Text = "Ativar Shader Visual"
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Click:Connect(function()
        Lighting.Brightness = 2
        Lighting.FogEnd = 600
        Lighting.Ambient = Color3.fromRGB(180, 180, 200)
        Lighting.OutdoorAmbient = Color3.fromRGB(220, 220, 240)
    end)
end
-- Clima
do
    local cont = tabContents[2]
    local btn = Instance.new("TextButton", cont)
    btn.Size = UDim2.new(0, 180, 0, 36)
    btn.Position = UDim2.new(0, 18, 0, 12)
    btn.Text = "Mudar Clima Aleatório"
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Click:Connect(function()
        Lighting.ClockTime = math.random(6, 20)
    end)
end
-- Som
do
    local cont = tabContents[3]
    local btn = Instance.new("TextButton", cont)
    btn.Size = UDim2.new(0, 180, 0, 36)
    btn.Position = UDim2.new(0, 18, 0, 12)
    btn.Text = "Ativar Som Ambiente"
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Click:Connect(function()
        local s = Instance.new("Sound", SoundService)
        s.SoundId = "rbxassetid://9127857986"
        s.Looped = true
        s.Volume = 0.4
        s:Play()
    end)
end
-- Partículas
do
    local cont = tabContents[4]
    local btn = Instance.new("TextButton", cont)
    btn.Size = UDim2.new(0, 180, 0, 36)
    btn.Position = UDim2.new(0, 18, 0, 12)
    btn.Text = "Ativar Partículas"
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Click:Connect(function()
        for _, part in pairs(workspace:GetDescendants()) do
            if part:IsA("BasePart") and part.Name == "ShaderEmitter" then
                local p = Instance.new("ParticleEmitter", part)
                p.Texture = "rbxassetid://243660364"
                p.Rate = 50
                p.Lifetime = NumberRange.new(1, 2)
                p.Speed = NumberRange.new(2, 4)
                p.VelocitySpread = 180
            end
        end
    end)
end

---------------------- Painel resumido (ícone flutuante arrastável) ----------------------
local openIcon = Instance.new("ImageButton", gui)
openIcon.Size = UDim2.new(0, 60, 0, 60)
openIcon.Position = UDim2.new(0, 18, 1, -78)
openIcon.BackgroundColor3 = Color3.fromRGB(22, 22, 26)
openIcon.Image = "https://i.imgur.com/Lv8xCuA.jpeg"
openIcon.Visible = true
openIcon.AutoButtonColor = true
local circle = Instance.new("UICorner", openIcon)
circle.CornerRadius = UDim.new(1, 0)
local stroke = Instance.new("UIStroke", openIcon)
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(0,0,0)
stroke.Transparency = 0.4
openIcon.Visible = false

-- Arrastar o ícone
local UIS = game:GetService("UserInputService")
local dragging, dragInput, dragStart, startPos

openIcon.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = openIcon.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)
openIcon.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        openIcon.Position = UDim2.new(
            startPos.X.Scale, math.clamp(startPos.X.Offset + delta.X, 0, gui.AbsoluteSize.X - openIcon.AbsoluteSize.X),
            startPos.Y.Scale, math.clamp(startPos.Y.Offset + delta.Y, 0, gui.AbsoluteSize.Y - openIcon.AbsoluteSize.Y)
        )
    end
end)

-- Duplo clique para reabrir o painel
local lastClick = 0
openIcon.MouseButton1Click:Connect(function()
    local now = tick()
    if now - lastClick < 0.3 then
        TweenService:Create(panel, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -245, 0.55, -162)}):Play()
        panel.Visible = true
        openIcon.Visible = false
    end
    lastClick = now
end)

---------------------- Tela de Key Inicial ----------------------
local keyPanel = Instance.new("Frame", gui)
keyPanel.Size = UDim2.new(0, 340, 0, 180)
keyPanel.Position = UDim2.new(0.5, -170, 0.5, -90)
keyPanel.BackgroundColor3 = Color3.fromRGB(20, 22, 40)
keyPanel.BorderSizePixel = 0
keyPanel.Visible = true
Instance.new("UICorner", keyPanel).CornerRadius = UDim.new(0, 14)
local keyGrad = Instance.new("UIGradient", keyPanel)
keyGrad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 220, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 100))
}
keyGrad.Rotation = 30

local keyTitle = Instance.new("TextLabel", keyPanel)
keyTitle.Size = UDim2.new(1, 0, 0, 34)
keyTitle.Position = UDim2.new(0, 0, 0, 16)
keyTitle.Text = "🔐 Digite sua Key"
keyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
keyTitle.BackgroundTransparency = 1
keyTitle.Font = Enum.Font.GothamBold
keyTitle.TextSize = 22

local keyInput = Instance.new("TextBox", keyPanel)
keyInput.Size = UDim2.new(0.75, 0, 0, 36)
keyInput.Position = UDim2.new(0.125, 0, 0, 60)
keyInput.PlaceholderText = "Digite a key aqui..."
keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
keyInput.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
keyInput.Font = Enum.Font.Gotham
keyInput.TextSize = 16
Instance.new("UICorner", keyInput).CornerRadius = UDim.new(0, 8)

local keySubmit = Instance.new("TextButton", keyPanel)
keySubmit.Size = UDim2.new(0.5, 0, 0, 34)
keySubmit.Position = UDim2.new(0.25, 0, 0, 116)
keySubmit.Text = "Entrar"
keySubmit.TextColor3 = Color3.fromRGB(255, 255, 255)
keySubmit.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
keySubmit.Font = Enum.Font.GothamBold
keySubmit.TextSize = 16
Instance.new("UICorner", keySubmit).CornerRadius = UDim.new(0, 8)

-- Tela de Key -> Painel principal após validação
keySubmit.MouseButton1Click:Connect(function()
    if keyInput.Text == "shaderlight" then
        TweenService:Create(keyPanel, TweenInfo.new(0.4), {Position = UDim2.new(0.5, -170, 1.1, 0)}):Play()
        task.wait(0.45)
        keyPanel.Visible = false
        TweenService:Create(panel, TweenInfo.new(0.4), {Position = UDim2.new(0.5, -245, 0.55, -162)}):Play()
        panel.Visible = true
    else
        keyInput.Text = ""
        keyInput.PlaceholderText = "Key inválida!"
        keyInput.PlaceholderColor3 = Color3.fromRGB(255, 0, 0)
    end
end)

-- Minimizar painel principal (mostrar ícone flutuante)
resumeBtn.MouseButton1Click:Connect(function()
    panel.Visible = false
    openIcon.Visible = true
end)