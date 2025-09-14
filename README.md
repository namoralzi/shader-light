local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local SoundService = game:GetService("SoundService")
local player = Players.LocalPlayer

-- Helper para criar gradiente animado
local function animateGradient(uiGradient, colors, speed)
    coroutine.wrap(function()
        while uiGradient and uiGradient.Parent do
            local t = tick() * speed
            uiGradient.Offset = Vector2.new(math.sin(t)*0.2, math.cos(t)*0.2)
            task.wait(0.03)
        end
    end)()
end

local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "ShaderLightUI"
gui.ResetOnSpawn = false

-- ÍCONE FLUTUANTE
local openIcon = Instance.new("ImageButton", gui)
openIcon.Size = UDim2.new(0, 60, 0, 60)
openIcon.Position = UDim2.new(0, 16, 1, -110) -- Sempre embaixo do ícone do Roblox
openIcon.BackgroundColor3 = Color3.fromRGB(32, 32, 36)
openIcon.Image = "https://i.imgur.com/Lv8xCuA.jpeg"
openIcon.Visible = false
openIcon.AutoButtonColor = true
openIcon.ZIndex = 101
local circle = Instance.new("UICorner", openIcon)
circle.CornerRadius = UDim.new(1, 0)
local stroke = Instance.new("UIStroke", openIcon)
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(0,0,0)
stroke.Transparency = 0.45

-- Arrastar o ícone flutuante
local dragging, dragStart, startPos
openIcon.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = openIcon.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
openIcon.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        openIcon.Position = UDim2.new(
            startPos.X.Scale, math.clamp(startPos.X.Offset + delta.X, 0, gui.AbsoluteSize.X - openIcon.AbsoluteSize.X),
            startPos.Y.Scale, math.clamp(startPos.Y.Offset + delta.Y, 0, gui.AbsoluteSize.Y - openIcon.AbsoluteSize.Y)
        )
    end
end)

-- PAINEL PRINCIPAL
local panel = Instance.new("Frame")
panel.Parent = gui
panel.Size = UDim2.new(0, 510, 0, 340)
panel.Position = UDim2.new(0.5, -255, 1.1, 0) -- Start offscreen
panel.BackgroundColor3 = Color3.fromRGB(18, 18, 30)
panel.BorderSizePixel = 0
panel.ClipsDescendants = true
panel.Visible = false
panel.ZIndex = 100

local grad = Instance.new("UIGradient", panel)
grad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(00, 36, 80)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(80, 0, 130)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 255, 200))
})
grad.Rotation = 35
animateGradient(grad, grad.Color, 0.6)
local panelCorner = Instance.new("UICorner", panel)
panelCorner.CornerRadius = UDim.new(0, 20)

-- Header
local header = Instance.new("Frame", panel)
header.Size = UDim2.new(1, 0, 0, 48)
header.BackgroundColor3 = Color3.fromRGB(22, 22, 36)
header.BorderSizePixel = 0
header.ClipsDescendants = true
header.ZIndex = 100
local headerCorner = Instance.new("UICorner", header)
headerCorner.CornerRadius = UDim.new(0, 20)
local headerGrad = Instance.new("UIGradient", header)
headerGrad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 100, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(180, 0, 180))
}
headerGrad.Rotation = 0
animateGradient(headerGrad, headerGrad.Color, 0.45)
local headerIcon = Instance.new("ImageLabel", header)
headerIcon.Size = UDim2.new(0, 36, 0, 36)
headerIcon.Position = UDim2.new(0, 10, 0.5, -18)
headerIcon.Image = "https://i.imgur.com/Lv8xCuA.jpeg"
headerIcon.BackgroundTransparency = 1
headerIcon.ZIndex = 101
local headerTitle = Instance.new("TextLabel", header)
headerTitle.Size = UDim2.new(0, 300, 1, 0)
headerTitle.Position = UDim2.new(0, 58, 0, 0)
headerTitle.Text = "ShaderLight"
headerTitle.Font = Enum.Font.GothamBold
headerTitle.TextSize = 20
headerTitle.TextColor3 = Color3.fromRGB(255,255,255)
headerTitle.BackgroundTransparency = 1
headerTitle.TextXAlignment = Enum.TextXAlignment.Left
headerTitle.ZIndex = 101
-- Botão de minimizar
local resumeBtn = Instance.new("TextButton", header)
resumeBtn.Size = UDim2.new(0, 36, 0, 36)
resumeBtn.Position = UDim2.new(1, -44, 0, 6)
resumeBtn.BackgroundTransparency = 1
resumeBtn.Text = "—"
resumeBtn.TextColor3 = Color3.fromRGB(230,230,230)
resumeBtn.Font = Enum.Font.GothamBlack
resumeBtn.TextSize = 30
resumeBtn.ZIndex = 102

-- Abas estilo Chrome (com ícones!)
local tabsFrame = Instance.new("Frame", panel)
tabsFrame.Size = UDim2.new(1, -24, 0, 38)
tabsFrame.Position = UDim2.new(0, 12, 0, 54)
tabsFrame.BackgroundTransparency = 1
tabsFrame.ZIndex = 101

local tabData = {
    {name = "Visual", icon = "rbxassetid://7733960981"},
    {name = "Clima", icon = "rbxassetid://7734053498"},
    {name = "Som", icon = "rbxassetid://7733989023"},
    {name = "Partículas", icon = "rbxassetid://7734028463"},
    {name = "FPS Boost", icon = "rbxassetid://7734070723"},
    {name = "Créditos", icon = "rbxassetid://7733764714"},
}
local tabButtons, tabContents = {}, {}

for i,data in ipairs(tabData) do
    local btn = Instance.new("TextButton", tabsFrame)
    btn.Size = UDim2.new(0, 124, 1, -7)
    btn.Position = UDim2.new(0, (i-1)*130, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(44, 44, 58)
    btn.Text = "   "..data.name
    btn.TextColor3 = Color3.fromRGB(220,220,240)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 15
    btn.AutoButtonColor = false
    btn.ZIndex = 102
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 12)
    local icon = Instance.new("ImageLabel", btn)
    icon.Size = UDim2.new(0, 22, 0, 22)
    icon.Position = UDim2.new(0, 6, 0.5, -11)
    icon.BackgroundTransparency = 1
    icon.Image = data.icon
    icon.ZIndex = btn.ZIndex + 1
    table.insert(tabButtons, btn)
    local cont = Instance.new("Frame", panel)
    cont.Size = UDim2.new(1, -24, 1, -108)
    cont.Position = UDim2.new(0, 12, 0, 98)
    cont.BackgroundTransparency = 1
    cont.Visible = false
    cont.ZIndex = 101
    table.insert(tabContents, cont)
end

for i,btn in ipairs(tabButtons) do
    btn.MouseButton1Click:Connect(function()
        for j,b in ipairs(tabButtons) do
            b.BackgroundColor3 = Color3.fromRGB(44,44,58)
            tabContents[j].Visible = false
        end
        btn.BackgroundColor3 = Color3.fromRGB(65,80,180)
        tabContents[i].Visible = true
    end)
end
tabButtons[1].BackgroundColor3 = Color3.fromRGB(65,80,180)
tabContents[1].Visible = true

-- Conteúdo de cada aba
do -- Visual
    local cont = tabContents[1]
    local btn1 = Instance.new("TextButton", cont)
    btn1.Size = UDim2.new(0, 190, 0, 36)
    btn1.Position = UDim2.new(0, 18, 0, 15)
    btn1.Text = "Ativar Shader Visual"
    btn1.TextColor3 = Color3.fromRGB(255,255,255)
    btn1.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn1.Font = Enum.Font.GothamBold
    btn1.TextSize = 15
    Instance.new("UICorner", btn1).CornerRadius = UDim.new(0, 8)
    btn1.MouseButton1Click:Connect(function()
        Lighting.Brightness = 2
        Lighting.FogEnd = 600
        Lighting.Ambient = Color3.fromRGB(180, 180, 200)
        Lighting.OutdoorAmbient = Color3.fromRGB(220, 220, 240)
    end)
end
do -- Clima
    local cont = tabContents[2]
    local btn2 = Instance.new("TextButton", cont)
    btn2.Size = UDim2.new(0, 180, 0, 36)
    btn2.Position = UDim2.new(0, 18, 0, 15)
    btn2.Text = "Mudar Clima Aleatório"
    btn2.TextColor3 = Color3.fromRGB(255,255,255)
    btn2.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn2.Font = Enum.Font.GothamBold
    btn2.TextSize = 15
    Instance.new("UICorner", btn2).CornerRadius = UDim.new(0, 8)
    btn2.MouseButton1Click:Connect(function()
        Lighting.ClockTime = math.random(6, 20)
    end)
end
do -- Som
    local cont = tabContents[3]
    local btn3 = Instance.new("TextButton", cont)
    btn3.Size = UDim2.new(0, 180, 0, 36)
    btn3.Position = UDim2.new(0, 18, 0, 15)
    btn3.Text = "Ativar Som Ambiente"
    btn3.TextColor3 = Color3.fromRGB(255,255,255)
    btn3.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn3.Font = Enum.Font.GothamBold
    btn3.TextSize = 15
    Instance.new("UICorner", btn3).CornerRadius = UDim.new(0, 8)
    btn3.MouseButton1Click:Connect(function()
        local s = Instance.new("Sound", SoundService)
        s.SoundId = "rbxassetid://9127857986"
        s.Looped = true
        s.Volume = 0.4
        s:Play()
    end)
end
do -- Partículas
    local cont = tabContents[4]
    local btn4 = Instance.new("TextButton", cont)
    btn4.Size = UDim2.new(0, 180, 0, 36)
    btn4.Position = UDim2.new(0, 18, 0, 15)
    btn4.Text = "Ativar Partículas"
    btn4.TextColor3 = Color3.fromRGB(255,255,255)
    btn4.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn4.Font = Enum.Font.GothamBold
    btn4.TextSize = 15
    Instance.new("UICorner", btn4).CornerRadius = UDim.new(0, 8)
    btn4.MouseButton1Click:Connect(function()
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
do -- FPS Boost
    local cont = tabContents[5]
    local btn5 = Instance.new("TextButton", cont)
    btn5.Size = UDim2.new(0, 180, 0, 36)
    btn5.Position = UDim2.new(0, 18, 0, 15)
    btn5.Text = "Ativar FPS Boost"
    btn5.TextColor3 = Color3.fromRGB(255,255,255)
    btn5.BackgroundColor3 = Color3.fromRGB(50, 70, 50)
    btn5.Font = Enum.Font.GothamBold
    btn5.TextSize = 15
    Instance.new("UICorner", btn5).CornerRadius = UDim.new(0, 8)
    btn5.MouseButton1Click:Connect(function()
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") then
                v.Material = Enum.Material.Plastic
                v.Reflectance = 0
            end
            if v:IsA("ParticleEmitter") or v:IsA("Trail") then
                v.Enabled = false
            end
        end
        Lighting.FogEnd = 1e5
        Lighting.Brightness = 1
    end)
end
do -- Créditos
    local cont = tabContents[6]
    local label = Instance.new("TextLabel", cont)
    label.Size = UDim2.new(1, -36, 0, 40)
    label.Position = UDim2.new(0, 18, 0, 20)
    label.BackgroundTransparency = 1
    label.Text = "ShaderLight gui - Feito por namoralzi\nDesign Copilot ✨"
    label.Font = Enum.Font.GothamBold
    label.TextSize = 19
    label.TextColor3 = Color3.fromRGB(200,240,255)
    label.TextWrapped = true
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextYAlignment = Enum.TextYAlignment.Top
end

-- ANIMAÇÃO DE ABRIR/FECHAR PAINEL
local function openPanelAnim()
    panel.Position = UDim2.new(0.5, -255, 1.1, 0)
    panel.Visible = true
    TweenService:Create(panel, TweenInfo.new(0.44, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), {Position = UDim2.new(0.5, -255, 0.55, -170)}):Play()
end
local function closePanelAnim()
    TweenService:Create(panel, TweenInfo.new(0.35, Enum.EasingStyle.Quint, Enum.EasingDirection.In), {Position = UDim2.new(0.5, -255, 1.1, 0)}):Play()
    task.wait(0.36)
    panel.Visible = false
end

-- Clique no ícone flutuante abre painel (NUNCA some)
openIcon.MouseButton1Click:Connect(function()
    if panel.Visible then return end
    openPanelAnim()
    task.wait(0.45)
    openIcon.Visible = false
end)
-- Minimizando: mostra ícone sempre
resumeBtn.MouseButton1Click:Connect(function()
    if not openIcon.Visible then openIcon.Visible = true end
    closePanelAnim()
end)

---------------------- TELA DE KEY ----------------------
local keyPanel = Instance.new("Frame", gui)
keyPanel.Size = UDim2.new(0, 360, 0, 190)
keyPanel.Position = UDim2.new(0.5, -180, 0.5, -95)
keyPanel.BackgroundColor3 = Color3.fromRGB(24, 28, 52)
keyPanel.BorderSizePixel = 0
keyPanel.Visible = true
keyPanel.ZIndex = 200
Instance.new("UICorner", keyPanel).CornerRadius = UDim.new(0, 14)
local keyGrad = Instance.new("UIGradient", keyPanel)
keyGrad.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 220, 255)),
    ColorSequenceKeypoint.new(0.42, Color3.fromRGB(100, 0, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 100))
}
keyGrad.Rotation = 38
animateGradient(keyGrad, keyGrad.Color, 0.7)

local keyTitle = Instance.new("TextLabel", keyPanel)
keyTitle.Size = UDim2.new(1, 0, 0, 32)
keyTitle.Position = UDim2.new(0, 0, 0, 14)
keyTitle.Text = "🔐 Digite sua Key"
keyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
keyTitle.BackgroundTransparency = 1
keyTitle.Font = Enum.Font.GothamBold
keyTitle.TextSize = 21

local keyInput = Instance.new("TextBox", keyPanel)
keyInput.Size = UDim2.new(0.75, 0, 0, 36)
keyInput.Position = UDim2.new(0.125, 0, 0, 56)
keyInput.PlaceholderText = "Digite a key aqui..."
keyInput.TextColor3 = Color3.fromRGB(255, 255, 255)
keyInput.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
keyInput.Font = Enum.Font.Gotham
keyInput.TextSize = 16
Instance.new("UICorner", keyInput).CornerRadius = UDim.new(0, 8)

local keySubmit = Instance.new("TextButton", keyPanel)
keySubmit.Size = UDim2.new(0.5, 0, 0, 34)
keySubmit.Position = UDim2.new(0.25, 0, 0, 110)
keySubmit.Text = "Entrar"
keySubmit.TextColor3 = Color3.fromRGB(255, 255, 255)
keySubmit.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
keySubmit.Font = Enum.Font.GothamBold
keySubmit.TextSize = 16
Instance.new("UICorner", keySubmit).CornerRadius = UDim.new(0, 8)
local keyState = Instance.new("TextLabel", keyPanel)
keyState.Size = UDim2.new(1, 0, 0, 18)
keyState.Position = UDim2.new(0, 0, 1, -20)
keyState.Text = ""
keyState.TextColor3 = Color3.fromRGB(255,80,80)
keyState.BackgroundTransparency = 1
keyState.Font = Enum.Font.Gotham
keyState.TextSize = 14

keySubmit.MouseButton1Click:Connect(function()
    if keyInput.Text:lower() == "shader" then
        TweenService:Create(keyPanel, TweenInfo.new(0.4), {Position = UDim2.new(0.5, -180, 1.2, 0)}):Play()
        task.wait(0.45)
        keyPanel.Visible = false
        openPanelAnim()
    else
        keyInput.Text = ""
        keyInput.PlaceholderText = "Key inválida!"
        keyInput.PlaceholderColor3 = Color3.fromRGB(255, 0, 0)
        keyState.Text = "Key incorreta. Tente novamente."
        TweenService:Create(keyPanel, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(60, 15, 25)}):Play()
        wait(0.17)
        TweenService:Create(keyPanel, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(24, 28, 52)}):Play()
    end
end)