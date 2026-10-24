-- AUTO FARM EXU V25 (Stable / Mobile / PC)
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- ==========================================
-- CONFIGURAÇÕES
-- ==========================================
local isFarming = false
local baseCFrame = nil
local currentSpeed = 500
local activeRarities = {}
local sortMode = "Mais Próximo"
local isRarityOpen = false
local isSortOpen = false
local rarityFrame, sortFrame

local playerChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local hrp = playerChar:WaitForChild("HumanoidRootPart")

local Remotes = ReplicatedStorage:WaitForChild("Remotes", 5)
local DropExuRemote = Remotes and Remotes:FindFirstChild("DropExu")
local PlaceExuRemote = Remotes and Remotes:FindFirstChild("PlaceExu")
local SellAllRemote = Remotes and Remotes:FindFirstChild("SellAll")

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
-- 1. INTERFACE (TAMANHO CELULAR)
-- ==========================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MidnightStable"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.DisplayOrder = 999
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Bolinha
local BallBtn = Instance.new("TextButton")
BallBtn.Size = UDim2.new(0, 50, 0, 50)
BallBtn.Position = UDim2.new(0, 15, 0, 100)
BallBtn.BackgroundColor3 = Colors.MainBG
BallBtn.BackgroundTransparency = 0.1
BallBtn.Text = "👻"
BallBtn.TextColor3 = Colors.Text
BallBtn.Font = Enum.Font.GothamBlack
BallBtn.TextSize = 24
BallBtn.Active = true
BallBtn.Draggable = true
BallBtn.Visible = false
BallBtn.ZIndex = 1000
BallBtn.Parent = ScreenGui
local UICornerBall = Instance.new("UICorner"); UICornerBall.CornerRadius = UDim.new(1, 0); UICornerBall.Parent = BallBtn
local UIStrokeBall = Instance.new("UIStroke"); UIStrokeBall.Color = Colors.Accent; UIStrokeBall.Thickness = 2; UIStrokeBall.Parent = BallBtn

-- Loading
local Loading = Instance.new("Frame")
Loading.Size = UDim2.new(0, 480, 0, 320)
Loading.Position = UDim2.new(0.5, -240, 0.5, -160)
Loading.BackgroundColor3 = Colors.MainBG
Loading.BackgroundTransparency = 0.1
Loading.Parent = ScreenGui
local UICornerL = Instance.new("UICorner"); UICornerL.CornerRadius = UDim.new(0, 12); UICornerL.Parent = Loading
local UIStrokeL = Instance.new("UIStroke"); UIStrokeL.Color = Colors.Accent; UIStrokeL.Thickness = 2; UIStrokeL.Parent = Loading

local LoadTxt = Instance.new("TextLabel")
LoadTxt.Size = UDim2.new(1, 0, 0, 30)
LoadTxt.Position = UDim2.new(0, 0, 0.5, -15)
LoadTxt.BackgroundTransparency = 1
LoadTxt.Text = "Carregando Midnight Hub..."
LoadTxt.TextColor3 = Colors.Text
LoadTxt.Font = Enum.Font.GothamBold
LoadTxt.TextSize = 16
LoadTxt.Parent = Loading

local LoadIcon = Instance.new("ImageLabel")
LoadIcon.Size = UDim2.new(0, 40, 0, 40)
LoadIcon.Position = UDim2.new(0.5, -20, 0.5, -55)
LoadIcon.BackgroundTransparency = 1
LoadIcon.Image = "rbxassetid://4965945816"
LoadIcon.ImageColor3 = Colors.Accent
LoadIcon.Parent = Loading

-- Main Frame
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 480, 0, 320)
MainFrame.Position = UDim2.new(0.5, -240, 0.5, -160)
MainFrame.BackgroundColor3 = Colors.MainBG
MainFrame.BackgroundTransparency = 0.1
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false
MainFrame.Parent = ScreenGui
local UICornerM = Instance.new("UICorner"); UICornerM.CornerRadius = UDim.new(0, 12); UICornerM.Parent = MainFrame
local UIStrokeM = Instance.new("UIStroke"); UIStrokeM.Color = Colors.Accent; UIStrokeM.Thickness = 1.5; UIStrokeM.Transparency = 0.2; UIStrokeM.Parent = MainFrame

local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 35)
TitleBar.BackgroundTransparency = 1
TitleBar.Parent = MainFrame

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, -100, 1, 0)
TitleText.Position = UDim2.new(0, 15, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "Midnight Stable Hub"
TitleText.TextColor3 = Colors.Text
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.Font = Enum.Font.GothamBold
TitleText.TextSize = 15
TitleText.Parent = TitleBar

local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 25, 0, 25)
MinBtn.Position = UDim2.new(1, -60, 0, 5)
MinBtn.BackgroundTransparency = 1
MinBtn.Text = "—"
MinBtn.TextColor3 = Colors.TextDim
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 18
MinBtn.Parent = TitleBar

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 25, 0, 25)
CloseBtn.Position = UDim2.new(1, -30, 0, 5)
CloseBtn.BackgroundTransparency = 1
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Colors.TextDim
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 14
CloseBtn.Parent = TitleBar

local function CloseHub()
    TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0.5, 0, 0.5, 0)}):Play()
    task.wait(0.3)
    MainFrame.Visible = false
    BallBtn.Visible = true
end

local function OpenHub()
    BallBtn.Visible = false
    MainFrame.Visible = true
    MainFrame.Size = UDim2.new(0, 0, 0, 0)
    MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back), {Size = UDim2.new(0, 480, 0, 320), Position = UDim2.new(0.5, -240, 0.5, -160)}):Play()
end

MinBtn.MouseButton1Click:Connect(CloseHub)
BallBtn.MouseButton1Click:Connect(OpenHub)
CloseBtn.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

-- Sidebar e Pages
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 120, 1, -45)
Sidebar.Position = UDim2.new(0, 10, 0, 40)
Sidebar.BackgroundColor3 = Colors.SideBG
Sidebar.BackgroundTransparency = 0.2
Sidebar.Parent = MainFrame
local UICornerSide = Instance.new("UICorner"); UICornerSide.CornerRadius = UDim.new(0, 8); UICornerSide.Parent = Sidebar

local Pages = {}
local ActiveBtn = nil

local function CreateTab(icon, text, pageName)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 30)
    btn.BackgroundColor3 = Colors.SideBG
    btn.BackgroundTransparency = 1
    btn.Text = "  " .. icon .. "  " .. text
    btn.TextColor3 = Colors.TextDim
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    btn.Parent = Sidebar
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 6); uic.Parent = btn
    
    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.new(1, -145, 1, -50)
    page.Position = UDim2.new(0, 135, 0, 40)
    page.BackgroundTransparency = 1
    page.Visible = false
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.ScrollBarThickness = 2
    page.Parent = MainFrame
    Pages[pageName] = page

    -- Adiciona UIListLayout para organizar tudo automaticamente
    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 8)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = page

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
SideLayout.Parent = Sidebar

local HomePage = CreateTab("🏠", "Home", "Home")
local FarmPage = CreateTab("⚔️", "Farm", "Farm")
local ConfigPage = CreateTab("⚙️", "Config", "Config")

-- ==========================================
-- 2. CONTEÚDO: HOME
-- ==========================================
local Welcome = Instance.new("TextLabel")
Welcome.Size = UDim2.new(1, 0, 0, 25)
Welcome.BackgroundTransparency = 1
Welcome.Text = "Bem-vindo!"
Welcome.TextColor3 = Colors.Text
Welcome.TextXAlignment = Enum.TextXAlignment.Left
Welcome.Font = Enum.Font.GothamBold
Welcome.TextSize = 16
Welcome.LayoutOrder = 1
Welcome.Parent = HomePage

local InfoBox = Instance.new("Frame")
InfoBox.Size = UDim2.new(1, -10, 0, 60)
InfoBox.BackgroundColor3 = Colors.CardBG
InfoBox.BackgroundTransparency = 0.4
InfoBox.LayoutOrder = 2
InfoBox.Parent = HomePage
local UICornerInfo = Instance.new("UICorner"); UICornerInfo.CornerRadius = UDim.new(0, 8); UICornerInfo.Parent = InfoBox

local InfoText = Instance.new("TextLabel")
InfoText.Size = UDim2.new(1, -20, 1, -20)
InfoText.Position = UDim2.new(0, 10, 0, 10)
InfoText.BackgroundTransparency = 1
InfoText.Text = "Hub otimizado. Layout automático sem bugs de sobreposição."
InfoText.TextColor3 = Colors.TextDim
InfoText.Font = Enum.Font.Gotham
InfoText.TextSize = 12
InfoText.TextWrapped = true
InfoText.TextXAlignment = Enum.TextXAlignment.Left
InfoText.Parent = InfoBox

-- ==========================================
-- 3. CONTEÚDO: FARM
-- ==========================================
local Status = Instance.new("TextLabel")
Status.Size = UDim2.new(1, 0, 0, 20)
Status.BackgroundTransparency = 1
Status.Text = "Status: Parado"
Status.TextColor3 = Colors.Accent
Status.TextXAlignment = Enum.TextXAlignment.Left
Status.Font = Enum.Font.GothamBold
Status.TextSize = 12
Status.LayoutOrder = 1
Status.Parent = FarmPage

local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(1, 0, 0, 30)
ToggleBtn.BackgroundColor3 = Colors.Green
ToggleBtn.BackgroundTransparency = 0.2
ToggleBtn.Text = "▶ INICIAR AUTO FARM"
ToggleBtn.TextColor3 = Colors.MainBG
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 13
ToggleBtn.LayoutOrder = 2
ToggleBtn.Parent = FarmPage
local uicTg = Instance.new("UICorner"); uicTg.CornerRadius = UDim.new(0, 8); uicTg.Parent = ToggleBtn

local SellBtn = Instance.new("TextButton")
SellBtn.Size = UDim2.new(1, 0, 0, 30)
SellBtn.BackgroundColor3 = Colors.CardBG
SellBtn.BackgroundTransparency = 0.4
SellBtn.Text = "💰 VENDER TUDO"
SellBtn.TextColor3 = Colors.Text
SellBtn.Font = Enum.Font.GothamBold
SellBtn.TextSize = 12
SellBtn.LayoutOrder = 3
SellBtn.Parent = FarmPage
local uicSell = Instance.new("UICorner"); uicSell.CornerRadius = UDim.new(0, 8); uicSell.Parent = SellBtn

-- BOTÃO RARIDADE
local RarityBtn = Instance.new("TextButton")
RarityBtn.Size = UDim2.new(1, 0, 0, 30)
RarityBtn.BackgroundColor3 = Colors.CardBG
RarityBtn.BackgroundTransparency = 0.4
RarityBtn.Text = "  ⭐ Selecionar Raridades ▼"
RarityBtn.TextColor3 = Colors.Text
RarityBtn.TextXAlignment = Enum.TextXAlignment.Left
RarityBtn.Font = Enum.Font.GothamBold
RarityBtn.TextSize = 12
RarityBtn.LayoutOrder = 4
RarityBtn.Parent = FarmPage
local uicRar = Instance.new("UICorner"); uicRar.CornerRadius = UDim.new(0, 8); uicRar.Parent = RarityBtn

-- BOTÃO ORDEM
local SortBtn = Instance.new("TextButton")
SortBtn.Size = UDim2.new(1, 0, 0, 30)
SortBtn.BackgroundColor3 = Colors.CardBG
SortBtn.BackgroundTransparency = 0.4
SortBtn.Text = "  📏 Ordem: " .. sortMode .. " ▼"
SortBtn.TextColor3 = Colors.Text
SortBtn.TextXAlignment = Enum.TextXAlignment.Left
SortBtn.Font = Enum.Font.GothamBold
SortBtn.TextSize = 12
SortBtn.LayoutOrder = 5
SortBtn.Parent = FarmPage
local uicSort = Instance.new("UICorner"); uicSort.CornerRadius = UDim.new(0, 8); uicSort.Parent = SortBtn

-- Lógica de Expandir (Empurrando o layout)
local function CreateToggle(parent, name, color, order)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(1, -10, 0, 30)
    card.BackgroundColor3 = Colors.CardBG
    card.BackgroundTransparency = 0.4
    card.LayoutOrder = order
    card.Parent = parent
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 6); uic.Parent = card

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -60, 1, 0)
    lbl.Position = UDim2.new(0, 10, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = name
    lbl.TextColor3 = Colors.Text
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Font = Enum.Font.GothamBold
    lbl.TextSize = 12
    lbl.Parent = card

    local tgBg = Instance.new("Frame")
    tgBg.Size = UDim2.new(0, 30, 0, 15)
    tgBg.Position = UDim2.new(1, -40, 0.5, -7.5)
    tgBg.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    tgBg.Parent = card
    local uicBg = Instance.new("UICorner"); uicBg.CornerRadius = UDim.new(1, 0); uicBg.Parent = tgBg

    local tgCir = Instance.new("Frame")
    tgCir.Size = UDim2.new(0, 11, 0, 11)
    tgCir.Position = UDim2.new(0, 2, 0.5, -5.5)
    tgCir.BackgroundColor3 = Colors.TextDim
    tgCir.Parent = tgBg
    local uicCir = Instance.new("UICorner"); uicCir.CornerRadius = UDim.new(1, 0); uicCir.Parent = tgCir

    local isOn = false
    card.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isOn = not isOn
            if isOn then
                TweenService:Create(tgBg, TweenInfo.new(0.2), {BackgroundColor3 = color}):Play()
                TweenService:Create(tgCir, TweenInfo.new(0.2), {Position = UDim2.new(1, -13, 0.5, -5.5), BackgroundColor3 = Colors.Text}):Play()
                activeRarities[name] = true
            else
                TweenService:Create(tgBg, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(60, 60, 80)}):Play()
                TweenService:Create(tgCir, TweenInfo.new(0.2), {Position = UDim2.new(0, 2, 0.5, -5.5), BackgroundColor3 = Colors.TextDim}):Play()
                activeRarities[name] = nil
            end
        end
    end)
end

RarityBtn.MouseButton1Click:Connect(function()
    isRarityOpen = not isRarityOpen
    if isRarityOpen then
        RarityBtn.Text = "  ⭐ Selecionar Raridades ▲"
        rarityFrame = Instance.new("Frame")
        rarityFrame.Size = UDim2.new(1, 0, 0, 200)
        rarityFrame.BackgroundTransparency = 1
        rarityFrame.LayoutOrder = 6 -- Fica logo abaixo do botão
        rarityFrame.Parent = FarmPage
        
        local layout = Instance.new("UIListLayout")
        layout.Padding = UDim.new(0, 5)
        layout.Parent = rarityFrame

        CreateToggle(rarityFrame, "Divino", Color3.fromRGB(255, 215, 0), 1)
        CreateToggle(rarityFrame, "Secreto", Color3.fromRGB(150, 150, 150), 2)
        CreateToggle(rarityFrame, "Mítico", Color3.fromRGB(200, 50, 200), 3)
        CreateToggle(rarityFrame, "Lendário", Color3.fromRGB(255, 165, 0), 4)
        CreateToggle(rarityFrame, "Épico", Color3.fromRGB(150, 50, 200), 5)
        CreateToggle(rarityFrame, "Raro", Color3.fromRGB(0, 150, 255), 6)
    else
        RarityBtn.Text = "  ⭐ Selecionar Raridades ▼"
        if rarityFrame then rarityFrame:Destroy() rarityFrame = nil end
    end
end)

SortBtn.MouseButton1Click:Connect(function()
    isSortOpen = not isSortOpen
    if isSortOpen then
        SortBtn.Text = "  📏 Ordem: " .. sortMode .. " ▲"
        sortFrame = Instance.new("Frame")
        sortFrame.Size = UDim2.new(1, 0, 0, 95)
        sortFrame.BackgroundTransparency = 1
        sortFrame.LayoutOrder = 7
        sortFrame.Parent = FarmPage

        local layout = Instance.new("UIListLayout")
        layout.Padding = UDim.new(0, 5)
        layout.Parent = sortFrame

        local function CreateSortOpt(text, mode, order)
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1, -10, 0, 25)
            btn.BackgroundColor3 = Colors.CardBG
            btn.Text = "  " .. text
            btn.TextColor3 = Colors.Text
            btn.TextXAlignment = Enum.TextXAlignment.Left
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 11
            btn.LayoutOrder = order
            btn.Parent = sortFrame
            local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 4); uic.Parent = btn
            btn.MouseButton1Click:Connect(function()
                sortMode = mode
                SortBtn.Text = "  📏 Ordem: " .. sortMode .. " ▼"
                isSortOpen = false
                if sortFrame then sortFrame:Destroy() sortFrame = nil end
            end)
        end

        CreateSortOpt("📍 Mais Próximo", "Mais Próximo", 1)
        CreateSortOpt("📉 Menor Valor ($)", "Menor Valor", 2)
        CreateSortOpt("📈 Maior Valor ($$$)", "Maior Valor", 3)
    else
        SortBtn.Text = "  📏 Ordem: " .. sortMode .. " ▼"
        if sortFrame then sortFrame:Destroy() sortFrame = nil end
    end
end)

-- ==========================================
-- 4. CONTEÚDO: CONFIG
-- ==========================================
local SpeedTxt = Instance.new("TextLabel")
SpeedTxt.Size = UDim2.new(1, 0, 0, 20)
SpeedTxt.BackgroundTransparency = 1
SpeedTxt.Text = "Velocidade: " .. currentSpeed
SpeedTxt.TextColor3 = Colors.Accent
SpeedTxt.TextXAlignment = Enum.TextXAlignment.Left
SpeedTxt.Font = Enum.Font.GothamBold
SpeedTxt.TextSize = 12
SpeedTxt.LayoutOrder = 1
SpeedTxt.Parent = ConfigPage

local SliderBg = Instance.new("Frame")
SliderBg.Size = UDim2.new(1, -20, 0, 8)
SliderBg.BackgroundColor3 = Colors.CardBG
SliderBg.BackgroundTransparency = 0.2
SliderBg.LayoutOrder = 2
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
        SpeedTxt.Text = "Velocidade: " .. currentSpeed
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
end)

-- ==========================================
-- 5. LÓGICA DE JOGO
-- ==========================================
local function GetExuData(model)
    local name = model.Name
    local rarity = "Comum"
    local money = 0
    local attrs = model:GetAttributes()
    for attrName, value in pairs(attrs) do
        local lowerName = string.lower(attrName)
        if string.find(lowerName, "rarity") or string.find(lowerName, "raridade") then 
            rarity = tostring(value) 
        elseif string.find(lowerName, "price") or string.find(lowerName, "value") or string.find(lowerName, "income") or string.find(lowerName, "money") then
            local numStr = string.match(tostring(value), "%d+")
            if numStr then money = tonumber(numStr) end
        end
    end
    if money == 0 then
        local numStr = string.match(name, "%d+")
        if numStr then money = tonumber(numStr) end
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
    return name, rarity, money
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
    Status.Text = "Base salva! Iniciando..."
    
    while isFarming do
        local successLoop, errorMsg = pcall(function()
            local validTargets = {}
            local hasFilter = false
            for _ in pairs(activeRarities) do hasFilter = true break end
            
            for _, obj in pairs(game.Workspace:GetDescendants()) do
                if obj:IsA("ProximityPrompt") and string.lower(obj.ActionText) == "roubar" then
                    local part = obj.Parent
                    local checkPart = part:IsA("BasePart") and part or part:FindFirstChildWhichIsA("BasePart")
                    if checkPart then
                        local model = part
                        if model.Parent and not model.Parent:IsA("Workspace") and model.Parent ~= game.Workspace then model = model.Parent end
                        
                        local _, rarity, money = GetExuData(model)
                        local lowerRarity = string.lower(rarity)
                        
                        local isAllowed = not hasFilter
                        if hasFilter then
                            for allowed, _ in pairs(activeRarities) do
                                if string.find(lowerRarity, string.lower(allowed)) then isAllowed = true; break end
                            end
                        end
                        
                        if isAllowed then
                            table.insert(validTargets, {
                                prompt = obj,
                                part = checkPart,
                                value = money,
                                distance = (hrp.Position - checkPart.Position).Magnitude
                            })
                        end
                    end
                end
            end

            if #validTargets > 0 then
                if sortMode == "Menor Valor" then
                    table.sort(validTargets, function(a, b) return a.value < b.value end)
                elseif sortMode == "Maior Valor" then
                    table.sort(validTargets, function(a, b) return a.value > b.value end)
                else
                    table.sort(validTargets, function(a, b) return a.distance < b.distance end)
                end

                local best = validTargets[1]
                Status.Text = "Roubando " .. best.part.Parent.Name .. " ($" .. best.value .. ")..."
                local grabbed = StealSpecificExu(best.part.Parent, best.prompt, best.part)
                
                if grabbed then
                    Status.Text = "✅ Colocando na base..."
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
                    Status.Text = "❌ Falha ao pegar."
                    task.wait(0.5)
                end
            else
                Status.Text = "Nenhum Exu encontrado."
                task.wait(1)
            end
        end)
        if not successLoop then Status.Text = "Erro. Reiniciando..." task.wait(1) end
    end
end

ToggleBtn.MouseButton1Click:Connect(function()
    if isFarming then
        isFarming = false
        ToggleBtn.Text = "▶ INICIAR AUTO FARM"
        TweenService:Create(ToggleBtn, TweenInfo.new(0.3), {BackgroundColor3 = Colors.Green}):Play()
        Status.Text = "Status: Parado"
    else
        task.spawn(StartAutoFarm)
    end
end)

SellBtn.MouseButton1Click:Connect(function()
    if SellAllRemote then
        Status.Text = "Vendendo..."
        pcall(function() SellAllRemote:FireServer() end)
    end
end)

LocalPlayer.CharacterAdded:Connect(function(char)
    playerChar = char
    hrp = char:WaitForChild("HumanoidRootPart")
    if isFarming then
        isFarming = false
        ToggleBtn.Text = "▶ INICIAR AUTO FARM"
        TweenService:Create(ToggleBtn, TweenInfo.new(0.3), {BackgroundColor3 = Colors.Green}):Play()
        Status.Text = "Você morreu! Parado."
    end
end)

-- ANIMAÇÃO FINAL
task.spawn(function()
    local rot = 0
    local spin = RunService.RenderStepped:Connect(function(dt)
        rot = rot + dt * 180
        LoadIcon.Rotation = rot
    end)
    task.wait(1.5) 
    spin:Disconnect()
    TweenService:Create(Loading, TweenInfo.new(0.4), {Size = UDim2.new(0, 0, 0, 0), Position = UDim2.new(0.5, 0, 0.5, 0), BackgroundTransparency = 1}):Play()
    task.wait(0.4)
    Loading:Destroy()
    OpenHub()
end)
