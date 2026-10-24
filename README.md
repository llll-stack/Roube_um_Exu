-- AUTO FARM EXU V22 (Midnight Glass Edition + Dropdown + Bolinha)
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- ==========================================
-- CONFIGURAÇÕES E VARIÁVEIS
-- ==========================================
local isFarming = false
local baseCFrame = nil
local currentSpeed = 500
local activeRarities = {}
local isDropdownOpen = false

local playerChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local hrp = playerChar:WaitForChild("HumanoidRootPart")

local Remotes = ReplicatedStorage:WaitForChild("Remotes", 5)
local DropExuRemote = Remotes and Remotes:FindFirstChild("DropExu")
local PlaceExuRemote = Remotes and Remotes:FindFirstChild("PlaceExu")
local SellAllRemote = Remotes and Remotes:FindFirstChild("SellAll")

-- ==========================================
-- CORES MIDNIGHT HUB (Com Transparência)
-- ==========================================
local Colors = {
    MainBG = Color3.fromRGB(25, 15, 45),
    SideBG = Color3.fromRGB(35, 20, 60),
    CardBG = Color3.fromRGB(50, 30, 85),
    Text = Color3.fromRGB(255, 255, 255),
    TextDim = Color3.fromRGB(170, 170, 190),
    Accent = Color3.fromRGB(138, 92, 246),
    Green = Color3.fromRGB(74, 222, 128),
    Red = Color3.fromRGB(248, 113, 113)
}

-- ==========================================
-- 1. INTERFACE GRÁFICA (Bolinha e Carregamento)
-- ==========================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MidnightExuGlass"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Bolinha Flutuante (Para abrir/fechar)
local BallBtn = Instance.new("TextButton")
BallBtn.Size = UDim2.new(0, 50, 0, 50)
BallBtn.Position = UDim2.new(0, 20, 0, 100)
BallBtn.BackgroundColor3 = Colors.MainBG
BallBtn.BackgroundTransparency = 0.2
BallBtn.Text = "👻"
BallBtn.TextColor3 = Colors.Text
BallBtn.Font = Enum.Font.GothamBlack
BallBtn.TextSize = 24
BallBtn.Active = true
BallBtn.Draggable = true
BallBtn.Visible = false
BallBtn.Parent = ScreenGui
local UICornerBall = Instance.new("UICorner"); UICornerBall.CornerRadius = UDim.new(1, 0); UICornerBall.Parent = BallBtn
local UIStrokeBall = Instance.new("UIStroke"); UIStrokeBall.Color = Colors.Accent; UIStrokeBall.Thickness = 2; UIStrokeBall.Parent = BallBtn

-- Tela de Carregamento
local LoadingFrame = Instance.new("Frame")
LoadingFrame.Size = UDim2.new(0, 650, 0, 420)
LoadingFrame.Position = UDim2.new(0.5, -325, 0.5, -210)
LoadingFrame.BackgroundColor3 = Colors.MainBG
LoadingFrame.BackgroundTransparency = 0.1
LoadingFrame.BorderSizePixel = 0
LoadingFrame.Parent = ScreenGui
local UICornerLoad = Instance.new("UICorner"); UICornerLoad.CornerRadius = UDim.new(0, 12); UICornerLoad.Parent = LoadingFrame
local UIStrokeLoad = Instance.new("UIStroke"); UIStrokeLoad.Color = Colors.Accent; UIStrokeLoad.Thickness = 2; UIStrokeLoad.Parent = LoadingFrame

local LoadText = Instance.new("TextLabel")
LoadText.Size = UDim2.new(1, 0, 0, 30)
LoadText.Position = UDim2.new(0, 0, 0.5, -15)
LoadText.BackgroundTransparency = 1
LoadText.Text = "Carregando Midnight Hub..."
LoadText.TextColor3 = Colors.Text
LoadText.Font = Enum.Font.GothamBold
LoadText.TextSize = 18
LoadText.Parent = LoadingFrame

local LoadIcon = Instance.new("ImageLabel")
LoadIcon.Size = UDim2.new(0, 50, 0, 50)
LoadIcon.Position = UDim2.new(0.5, -25, 0.5, -60)
LoadIcon.BackgroundTransparency = 1
LoadIcon.Image = "rbxassetid://4965945816"
LoadIcon.ImageColor3 = Colors.Accent
LoadIcon.Parent = LoadingFrame

-- Janela Principal (Semi-transparente)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 650, 0, 420)
MainFrame.Position = UDim2.new(0.5, -325, 0.5, -210)
MainFrame.BackgroundColor3 = Colors.MainBG
MainFrame.BackgroundTransparency = 0.15 -- Deixa o Hub Transparente
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false
MainFrame.Parent = ScreenGui
local UICornerMain = Instance.new("UICorner"); UICornerMain.CornerRadius = UDim.new(0, 12); UICornerMain.Parent = MainFrame
local UIStrokeMain = Instance.new("UIStroke"); UIStrokeMain.Color = Colors.Accent; UIStrokeMain.Thickness = 1.5; UIStrokeMain.Transparency = 0.2; UIStrokeMain.Parent = MainFrame

-- Barra de Título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 45)
TitleBar.BackgroundTransparency = 1
TitleBar.Parent = MainFrame

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, -150, 1, 0)
TitleText.Position = UDim2.new(0, 20, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "Midnight Hub"
TitleText.TextColor3 = Colors.Text
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.Font = Enum.Font.GothamBold
TitleText.TextSize = 18
TitleText.Parent = TitleBar

-- Botão Minimizar
local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 25, 0, 25)
MinBtn.Position = UDim2.new(1, -80, 0, 10)
MinBtn.BackgroundTransparency = 1
MinBtn.Text = "—"
MinBtn.TextColor3 = Colors.TextDim
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 20
MinBtn.Parent = TitleBar

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 25, 0, 25)
CloseBtn.Position = UDim2.new(1, -50, 0, 10)
CloseBtn.BackgroundTransparency = 1
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Colors.TextDim
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 14
CloseBtn.Parent = TitleBar

-- Lógica de Abrir/Fechar (Bolinha e Botões)
local function CloseHub()
    local tweenOut = TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0.5, 0, 0.5, 0)})
    tweenOut:Play()
    tweenOut.Completed:Wait()
    MainFrame.Visible = false
    BallBtn.Visible = true
end

local function OpenHub()
    BallBtn.Visible = false
    MainFrame.Visible = true
    MainFrame.Size = UDim2.new(0, 0, 0, 0)
    MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    
    local tweenIn = TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 650, 0, 420), Position = UDim2.new(0.5, -325, 0.5, -210)})
    tweenIn:Play()
end

MinBtn.MouseButton1Click:Connect(CloseHub)
BallBtn.MouseButton1Click:Connect(OpenHub)
CloseBtn.MouseButton1Click:Connect(function() 
    ScreenGui:Destroy() 
end)

-- ==========================================
-- 2. SIDEBAR E PÁGINAS
-- ==========================================
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 160, 1, -60)
Sidebar.Position = UDim2.new(0, 10, 0, 50)
Sidebar.BackgroundColor3 = Colors.SideBG
Sidebar.BackgroundTransparency = 0.2 -- Transparente
Sidebar.Parent = MainFrame
local UICornerSide = Instance.new("UICorner"); UICornerSide.CornerRadius = UDim.new(0, 8); UICornerSide.Parent = Sidebar

local Pages = {}
local ActiveBtn = nil

local function CreateSidebarTab(icon, text, pageName)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 35)
    btn.BackgroundColor3 = Colors.SideBG
    btn.BackgroundTransparency = 1
    btn.Text = "  " .. icon .. "  " .. text
    btn.TextColor3 = Colors.TextDim
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.Parent = Sidebar
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 6); uic.Parent = btn
    
    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.new(1, -190, 1, -60)
    page.Position = UDim2.new(0, 180, 0, 50)
    page.BackgroundTransparency = 1
    page.Visible = false
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.ScrollBarThickness = 3
    page.ScrollBarImageColor3 = Colors.Accent
    page.Parent = MainFrame
    Pages[pageName] = page

    btn.MouseButton1Click:Connect(function()
        for _, p in pairs(Pages) do p.Visible = false end
        page.Visible = true
        
        for _, b in pairs(Sidebar:GetChildren()) do
            if b:IsA("TextButton") then
                b.BackgroundTransparency = 1
                b.TextColor3 = Colors.TextDim
            end
        end
        btn.BackgroundColor3 = Colors.CardBG
        btn.BackgroundTransparency = 0.3
        btn.TextColor3 = Colors.Accent
    end)
    
    if not ActiveBtn then
        ActiveBtn = btn
        page.Visible = true
        btn.BackgroundColor3 = Colors.CardBG
        btn.BackgroundTransparency = 0.3
        btn.TextColor3 = Colors.Accent
    end
    return page
end

local SideLayout = Instance.new("UIListLayout")
SideLayout.Padding = UDim.new(0, 5)
SideLayout.SortOrder = Enum.SortOrder.LayoutOrder
SideLayout.Parent = Sidebar

local HomePage = CreateSidebarTab("🏠", "Home", "Home")
local FarmPage = CreateSidebarTab("⚔️", "Farm", "Farm")
local ConfigPage = CreateSidebarTab("⚙️", "Config", "Config")

-- Conteúdo: HOME
local WelcomeLabel = Instance.new("TextLabel")
WelcomeLabel.Size = UDim2.new(1, 0, 0, 40)
WelcomeLabel.BackgroundTransparency = 1
WelcomeLabel.Text = "Bem-vindo ao Midnight Glass"
WelcomeLabel.TextColor3 = Colors.Text
WelcomeLabel.TextXAlignment = Enum.TextXAlignment.Left
WelcomeLabel.Font = Enum.Font.GothamBold
WelcomeLabel.TextSize = 22
WelcomeLabel.Parent = HomePage

local InfoBox = Instance.new("Frame")
InfoBox.Size = UDim2.new(1, -10, 0, 80)
InfoBox.Position = UDim2.new(0, 0, 0, 50)
InfoBox.BackgroundColor3 = Colors.CardBG
InfoBox.BackgroundTransparency = 0.4
InfoBox.Parent = HomePage
local UICornerInfo = Instance.new("UICorner"); UICornerInfo.CornerRadius = UDim.new(0, 8); UICornerInfo.Parent = InfoBox

local InfoText = Instance.new("TextLabel")
InfoText.Size = UDim2.new(1, -20, 1, -20)
InfoText.Position = UDim2.new(0, 10, 0, 10)
InfoText.BackgroundTransparency = 1
InfoText.Text = "Script atualizado com visual Glassmorphism, filtro Dropdown e botão flutuante."
InfoText.TextColor3 = Colors.TextDim
InfoText.Font = Enum.Font.Gotham
InfoText.TextSize = 13
InfoText.TextWrapped = true
InfoText.TextXAlignment = Enum.TextXAlignment.Left
InfoText.TextYAlignment = Enum.TextYAlignment.Top
InfoText.Parent = InfoBox

-- ==========================================
-- 3. CONTEÚDO: FARM (Com Filtro Dropdown)
-- ==========================================
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(1, 0, 0, 25)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "Status: Aguardando..."
StatusLabel.TextColor3 = Colors.Accent
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.Font = Enum.Font.GothamBold
StatusLabel.TextSize = 13
StatusLabel.Parent = FarmPage

local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(1, 0, 0, 35)
ToggleBtn.Position = UDim2.new(0, 0, 0, 30)
ToggleBtn.BackgroundColor3 = Colors.Green
ToggleBtn.BackgroundTransparency = 0.2
ToggleBtn.Text = "▶ INICIAR AUTO FARM"
ToggleBtn.TextColor3 = Colors.MainBG
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 14
ToggleBtn.Parent = FarmPage
local uicTg = Instance.new("UICorner"); uicTg.CornerRadius = UDim.new(0, 8); uicTg.Parent = ToggleBtn

local SellBtn = Instance.new("TextButton")
SellBtn.Size = UDim2.new(1, 0, 0, 30)
SellBtn.Position = UDim2.new(0, 0, 0, 75)
SellBtn.BackgroundColor3 = Colors.CardBG
SellBtn.BackgroundTransparency = 0.4
SellBtn.Text = "💰 VENDER TUDO (SellAll)"
SellBtn.TextColor3 = Colors.Text
SellBtn.Font = Enum.Font.GothamBold
SellBtn.TextSize = 12
SellBtn.Parent = FarmPage
local uicSell = Instance.new("UICorner"); uicSell.CornerRadius = UDim.new(0, 8); uicSell.Parent = SellBtn

-- SISTEMA DROPDOWN DE RARIDADE
local DropdownBtn = Instance.new("TextButton")
DropdownBtn.Size = UDim2.new(1, 0, 0, 35)
DropdownBtn.Position = UDim2.new(0, 0, 0, 120)
DropdownBtn.BackgroundColor3 = Colors.CardBG
DropdownBtn.BackgroundTransparency = 0.4
DropdownBtn.Text = "  ⭐ Selecionar Raridades ▼"
DropdownBtn.TextColor3 = Colors.Text
DropdownBtn.TextXAlignment = Enum.TextXAlignment.Left
DropdownBtn.Font = Enum.Font.GothamBold
DropdownBtn.TextSize = 13
DropdownBtn.Parent = FarmPage
local uicDrop = Instance.new("UICorner"); uicDrop.CornerRadius = UDim.new(0, 8); uicDrop.Parent = DropdownBtn

local DropdownContainer = Instance.new("Frame")
DropdownContainer.Size = UDim2.new(1, 0, 0, 0) -- Começa fechado (altura 0)
DropdownContainer.Position = UDim2.new(0, 0, 0, 160)
DropdownContainer.BackgroundTransparency = 1
DropdownContainer.ClipsDescendants = true -- Esconde os itens que passam do limite
DropdownContainer.Parent = FarmPage

local DropLayout = Instance.new("UIListLayout")
DropLayout.Padding = UDim.new(0, 5)
DropLayout.Parent = DropdownContainer

-- Função para animar o Dropdown
DropdownBtn.MouseButton1Click:Connect(function()
    isDropdownOpen = not isDropdownOpen
    if isDropdownOpen then
        DropdownBtn.Text = "  ⭐ Selecionar Raridades ▲"
        TweenService:Create(DropdownContainer, TweenInfo.new(0.3), {Size = UDim2.new(1, 0, 0, 210)}):Play()
    else
        DropdownBtn.Text = "  ⭐ Selecionar Raridades ▼"
        TweenService:Create(DropdownContainer, TweenInfo.new(0.3), {Size = UDim2.new(1, 0, 0, 0)}):Play()
    end
end)

-- Criando os Toggles dentro do Dropdown
local function CreateCardToggle(name, color)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(1, -10, 0, 30)
    card.BackgroundColor3 = Colors.CardBG
    card.BackgroundTransparency = 0.4
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 6); uic.Parent = card

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -60, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Colors.Text
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Font = Enum.Font.GothamBold
    label.TextSize = 12
    label.Parent = card

    local toggleBg = Instance.new("Frame")
    toggleBg.Size = UDim2.new(0, 30, 0, 15)
    toggleBg.Position = UDim2.new(1, -40, 0.5, -7.5)
    toggleBg.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    toggleBg.Parent = card
    local uicTgBg = Instance.new("UICorner"); uicTgBg.CornerRadius = UDim.new(1, 0); uicTgBg.Parent = toggleBg

    local toggleCircle = Instance.new("Frame")
    toggleCircle.Size = UDim2.new(0, 11, 0, 11)
    toggleCircle.Position = UDim2.new(0, 2, 0.5, -5.5)
    toggleCircle.BackgroundColor3 = Colors.TextDim
    toggleCircle.Parent = toggleBg
    local uicTgCir = Instance.new("UICorner"); uicTgCir.CornerRadius = UDim.new(1, 0); uicTgCir.Parent = toggleCircle

    local isActive = false
    card.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isActive = not isActive
            if isActive then
                TweenService:Create(toggleBg, TweenInfo.new(0.2), {BackgroundColor3 = color}):Play()
                TweenService:Create(toggleCircle, TweenInfo.new(0.2), {Position = UDim2.new(1, -13, 0.5, -5.5), BackgroundColor3 = Colors.Text}):Play()
                activeRarities[name] = true
            else
                TweenService:Create(toggleBg, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 80)}):Play()
                TweenService:Create(toggleCircle, TweenInfo.new(0.2), {Position = UDim2.new(0, 2, 0.5, -5.5), BackgroundColor3 = Colors.TextDim}):Play()
                activeRarities[name] = nil
            end
        end
    end)
    return card
end

local divinoToggle = CreateCardToggle("Divino", Color3.fromRGB(255, 215, 0)); divinoToggle.Parent = DropdownContainer
local secretoToggle = CreateCardToggle("Secreto", Color3.fromRGB(150, 150, 150)); secretoToggle.Parent = DropdownContainer
local miticoToggle = CreateCardToggle("Mítico", Color3.fromRGB(200, 50, 200)); miticoToggle.Parent = DropdownContainer
local lendarioToggle = CreateCardToggle("Lendário", Color3.fromRGB(255, 165, 0)); lendarioToggle.Parent = DropdownContainer
local epicoToggle = CreateCardToggle("Épico", Color3.fromRGB(150, 50, 200)); epicoToggle.Parent = DropdownContainer
local raroToggle = CreateCardToggle("Raro", Color3.fromRGB(0, 150, 255)); raroToggle.Parent = DropdownContainer

-- Ações dos botões
ToggleBtn.MouseButton1Click:Connect(function()
    if isFarming then
        isFarming = false
        ToggleBtn.Text = "▶ INICIAR AUTO FARM"
        TweenService:Create(ToggleBtn, TweenInfo.new(0.3), {BackgroundColor3 = Colors.Green}):Play()
        StatusLabel.Text = "Status: Parado"
    else
        task.spawn(StartAutoFarm)
    end
end)

SellBtn.MouseButton1Click:Connect(function()
    if SellAllRemote then
        StatusLabel.Text = "Vendendo todos os Exus..."
        pcall(function() SellAllRemote:FireServer() end)
    end
end)

-- ==========================================
-- 4. CONTEÚDO: CONFIG (Slider)
-- ==========================================
local SpeedTitle = Instance.new("TextLabel")
SpeedTitle.Size = UDim2.new(1, 0, 0, 30)
SpeedTitle.BackgroundTransparency = 1
SpeedTitle.Text = "Velocidade de Voo:"
SpeedTitle.TextColor3 = Colors.Text
SpeedTitle.TextXAlignment = Enum.TextXAlignment.Left
SpeedTitle.Font = Enum.Font.GothamBold
SpeedTitle.TextSize = 14
SpeedTitle.Parent = ConfigPage

local SpeedVal = Instance.new("TextLabel")
SpeedVal.Size = UDim2.new(1, 0, 0, 20)
SpeedVal.Position = UDim2.new(0, 0, 0, 25)
SpeedVal.BackgroundTransparency = 1
SpeedVal.Text = tostring(currentSpeed)
SpeedVal.TextColor3 = Colors.Accent
SpeedVal.TextXAlignment = Enum.TextXAlignment.Left
SpeedVal.Font = Enum.Font.GothamBold
SpeedVal.TextSize = 12
SpeedVal.Parent = ConfigPage

local SliderBg = Instance.new("Frame")
SliderBg.Size = UDim2.new(1, -20, 0, 8)
SliderBg.Position = UDim2.new(0, 0, 0, 55)
SliderBg.BackgroundColor3 = Colors.CardBG
SliderBg.BackgroundTransparency = 0.2
SliderBg.Parent = ConfigPage
local uicSlBg = Instance.new("UICorner"); uicSlBg.CornerRadius = UDim.new(1, 0); uicSlBg.Parent = SliderBg

local SliderFill = Instance.new("Frame")
SliderFill.Size = UDim2.new((currentSpeed - 100) / 9900, 0, 1, 0)
SliderFill.BackgroundColor3 = Colors.Accent
SliderFill.Parent = SliderBg
local uicSlFill = Instance.new("UICorner"); uicSlFill.CornerRadius = UDim.new(1, 0); uicSlFill.Parent = SliderFill

local SliderBtn = Instance.new("TextButton")
SliderBtn.Size = UDim2.new(1, 0, 1, 0)
SliderBtn.BackgroundTransparency = 1
SliderBtn.Text = ""
SliderBtn.Parent = SliderBg

local dragging = false
SliderBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = true end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local mouseX = input.Position.X
        local trackPos = SliderBg.AbsolutePosition.X
        local trackSize = SliderBg.AbsoluteSize.X
        local relativeX = math.clamp((mouseX - trackPos) / trackSize, 0, 1)
        
        currentSpeed = math.floor((relativeX * 9900) + 100)
        SliderFill.Size = UDim2.new(relativeX, 0, 1, 0)
        SpeedVal.Text = tostring(currentSpeed)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
end)

-- ==========================================
-- 5. LÓGICA DO JOGO
-- ==========================================
local function GetExuData(model)
    local name = model.Name
    local rarity = "Comum"
    local attrs = model:GetAttributes()
    for attrName, value in pairs(attrs) do
        local lowerName = string.lower(attrName)
        if string.find(lowerName, "rarity") or string.find(lowerName, "raridade") then rarity = tostring(value) end
    end
    if rarity == "Comum" then
        local lowerName = string.lower(name)
        if string.find(lowerName, "divin") then rarity = "Divino"
        elseif string.find(lowerName, "secret") or string.find(lowerName, "secreto") then rarity = "Secreto"
        elseif string.find(lowerName, "mitic") or string.find(lowerName, "mitico") then rarity = "Mítico"
        elseif string.find(lowerName, "lend") or string.find(lowerName, "legend") then rarity = "Lendário"
        elseif string.find(lowerName, "epic") or string.find(lowerName, "epico") then rarity = "Épico"
        elseif string.find(lowerName, "rare") or string.find(lowerName, "raro") then rarity = "Raro"
        elseif string.find(lowerName, "incom") then rarity = "Incomum"
        end
    end
    return name, rarity
end

local function IsHoldingExu()
    local char = LocalPlayer.Character
    local backpack = LocalPlayer.Backpack
    if char then for _, v in pairs(char:GetChildren()) do if v:IsA("Tool") then return true end end end
    if backpack then for _, v in pairs(backpack:GetChildren()) do if v:IsA("Tool") then return true end end end
    return false
end

local function FlyTo(targetCFrame, speed)
    if not hrp or not hrp.Parent then return end
    local distance = (hrp.Position - targetCFrame.Position).Magnitude
    local timeToFly = distance / (speed or 500)
    if timeToFly > 3 then timeToFly = 3 end 
    if timeToFly < 0.1 then timeToFly = 0.1 end
    local tween = TweenService:Create(hrp, TweenInfo.new(timeToFly, Enum.EasingStyle.Linear), {CFrame = targetCFrame})
    tween:Play()
    tween.Completed:Wait()
end

local function ReturnToBase()
    if hrp and hrp.Parent and baseCFrame then
        hrp.CFrame = baseCFrame + Vector3.new(0, 5, 0)
        task.wait(0.5) 
    end
end

local function StealSpecificExu(model, prompt, exuPart)
    FlyTo(exuPart.CFrame + Vector3.new(0, 3, 0), currentSpeed)
    task.wait(0.2)
    
    pcall(function() if prompt.HoldDuration > 0 then prompt.HoldDuration = 0 end end)
    
    pcall(function() 
        fireproximityprompt(prompt)
        if DropExuRemote then 
            DropExuRemote:FireServer(exuPart)
            DropExuRemote:FireServer(exuPart.Position)
        end
    end)
    
    pcall(function()
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
        task.wait(0.05)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
    end)

    local grabbed = false
    local timeout = tick() + 2.5
    while tick() < timeout do
        if IsHoldingExu() then grabbed = true; break end
        task.wait(0.05)
    end
    
    ReturnToBase()
    return grabbed
end

function StartAutoFarm()
    if isFarming then return end
    isFarming = true
    ToggleBtn.Text = "⏹ PARAR AUTO FARM"
    TweenService:Create(ToggleBtn, TweenInfo.new(0.3), {BackgroundColor3 = Colors.Red}):Play()
    
    baseCFrame = hrp.CFrame
    StatusLabel.Text = "Base salva! Iniciando..."
    
    while isFarming do
        local successLoop, errorMsg = pcall(function()
            local closestPrompt, closestPart = nil, nil
            local minDistance = math.huge
            local hasFilter = false
            for _ in pairs(activeRarities) do hasFilter = true break end
            
            for _, obj in pairs(game.Workspace:GetDescendants()) do
                if obj:IsA("ProximityPrompt") and string.lower(obj.ActionText) == "roubar" then
                    local part = obj.Parent
                    local checkPart = part:IsA("BasePart") and part or part:FindFirstChildWhichIsA("BasePart")
                    if checkPart then
                        local model = part
                        if model.Parent and not model.Parent:IsA("Workspace") and model.Parent ~= game.Workspace then model = model.Parent end
                        local _, rarity = GetExuData(model)
                        local lowerRarity = string.lower(rarity)
                        
                        local isAllowed = not hasFilter
                        if hasFilter then
                            for allowed, _ in pairs(activeRarities) do
                                if string.find(lowerRarity, string.lower(allowed)) then isAllowed = true; break end
                            end
                        end
                        
                        if isAllowed then
                            local distance = (hrp.Position - checkPart.Position).Magnitude
                            if distance < minDistance then
                                minDistance = distance
                                closestPrompt = obj
                                closestPart = checkPart
                            end
                        end
                    end
                end
            end

            if closestPrompt and closestPart then
                StatusLabel.Text = "Roubando " .. closestPart.Parent.Name .. "..."
                local grabbed = StealSpecificExu(closestPart.Parent, closestPrompt, closestPart)
                
                if grabbed then
                    StatusLabel.Text = "✅ Colocando na base..."
                    if PlaceExuRemote then
                        pcall(function() PlaceExuRemote:FireServer(baseCFrame.Position); PlaceExuRemote:FireServer() end)
                    end
                    
                    local basePrompt = nil
                    for _, obj in pairs(game.Workspace:GetDescendants()) do
                        if obj:IsA("ProximityPrompt") then
                            local action = string.lower(obj.ActionText)
                            if action == "colocar" or action == "place" or action == "dropar" then
                                if obj.Parent and obj.Parent:IsA("BasePart") then
                                    if (obj.Parent.Position - baseCFrame.Position).Magnitude < 30 then basePrompt = obj; break end
                                end
                            end
                        end
                    end
                    if basePrompt then pcall(function() fireproximityprompt(basePrompt) end) end
                    task.wait(0.5)
                else
                    StatusLabel.Text = "❌ Falha ao pegar."
                    task.wait(0.5)
                end
            else
                StatusLabel.Text = "Nenhum Exu encontrado."
                task.wait(1)
            end
        end)
        
        if not successLoop then
            StatusLabel.Text = "Erro detectado, reiniciando..."
            task.wait(1)
        end
    end
end

LocalPlayer.CharacterAdded:Connect(function(char)
    playerChar = char
    hrp = char:WaitForChild("HumanoidRootPart")
    if isFarming then
        isFarming = false
        ToggleBtn.Text = "▶ INICIAR AUTO FARM"
        TweenService:Create(ToggleBtn, TweenInfo.new(0.3), {BackgroundColor3 = Colors.Green}):Play()
        StatusLabel.Text = "Você morreu! Farm parado."
    end
end)

-- ==========================================
-- 6. ANIMAÇÃO DE CARREGAMENTO E ABERTURA
-- ==========================================
task.spawn(function()
    local rot = 0
    local spin = RunService.RenderStepped:Connect(function(dt)
        rot = rot + dt * 180
        LoadIcon.Rotation = rot
    end)
    
    task.wait(1.5) 
    spin:Disconnect()
    
    local tweenOut = TweenService:Create(LoadingFrame, TweenInfo.new(0.4), {Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0.5, 0, 0.5, 0), BackgroundTransparency = 1})
    tweenOut:Play()
    tweenOut.Completed:Wait()
    LoadingFrame:Destroy()
    
    MainFrame.Visible = true
    MainFrame.Size = UDim2.new(0, 0, 0, 0)
    MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    
    local tweenIn = TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 650, 0, 420), Position = UDim2.new(0.5, -325, 0.5, -210)})
    tweenIn:Play()
end)
