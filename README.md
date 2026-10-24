-- AUTO FARM EXU V16 (Universal PC/Mobile + Lógica V5 + UI Premium)
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- ==========================================
-- 1. CONFIGURAÇÕES
-- ==========================================
local isFarming = false
local baseCFrame = nil
local playerChar = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local hrp = playerChar:WaitForChild("HumanoidRootPart")
local activeRarities = {}

local Remotes = ReplicatedStorage:WaitForChild("Remotes", 5)
local DropExuRemote = Remotes and Remotes:FindFirstChild("DropExu")
local PlaceExuRemote = Remotes and Remotes:FindFirstChild("PlaceExu")
local SellAllRemote = Remotes and Remotes:FindFirstChild("SellAll")

-- ==========================================
-- 2. INTERFACE GRÁFICA (NEON PREMIUM)
-- ==========================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AutoFarmExuV16"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local BallBtn = Instance.new("TextButton")
BallBtn.Size = UDim2.new(0, 55, 0, 55)
BallBtn.Position = UDim2.new(0, 20, 0, 100)
BallBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
BallBtn.Text = "EXU"
BallBtn.TextColor3 = Color3.fromRGB(0, 255, 255)
BallBtn.Font = Enum.Font.GothamBlack
BallBtn.TextSize = 15
BallBtn.Active = true
BallBtn.Draggable = true
BallBtn.Visible = false
BallBtn.Parent = ScreenGui
local UICornerBall = Instance.new("UICorner"); UICornerBall.CornerRadius = UDim.new(1, 0); UICornerBall.Parent = BallBtn
local UIStrokeBall = Instance.new("UIStroke"); UIStrokeBall.Color = Color3.fromRGB(0, 255, 255); UIStrokeBall.Thickness = 2; UIStrokeBall.Parent = BallBtn

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 520, 0, 460)
MainFrame.Position = UDim2.new(0.5, -260, 0.5, -230)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui
local UICornerMain = Instance.new("UICorner"); UICornerMain.CornerRadius = UDim.new(0, 12); UICornerMain.Parent = MainFrame
local UIStrokeMain = Instance.new("UIStroke"); UIStrokeMain.Color = Color3.fromRGB(0, 150, 255); UIStrokeMain.Thickness = 1.5; UIStrokeMain.Parent = MainFrame

local bgGradient = Instance.new("UIGradient")
bgGradient.Color = ColorSequence.new{ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 25, 35)), ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 10, 15))}
bgGradient.Rotation = 45
bgGradient.Parent = MainFrame

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
Title.Text = "👻 AUTO FARM EXU - V16 UNIVERSAL"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 15
Title.Parent = MainFrame
local UICornerTitle = Instance.new("UICorner"); UICornerTitle.CornerRadius = UDim.new(0, 12); UICornerTitle.Parent = Title

local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Size = UDim2.new(0, 35, 0, 30)
MinimizeBtn.Position = UDim2.new(1, -45, 0, 5)
MinimizeBtn.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
MinimizeBtn.Text = "—"
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeBtn.Font = Enum.Font.GothamBlack
MinimizeBtn.TextSize = 18
MinimizeBtn.Parent = MainFrame
local UICornerMin = Instance.new("UICorner"); UICornerMin.CornerRadius = UDim.new(0, 8); UICornerMin.Parent = MinimizeBtn

MinimizeBtn.MouseButton1Click:Connect(function() MainFrame.Visible = false; BallBtn.Visible = true end)
BallBtn.MouseButton1Click:Connect(function() MainFrame.Visible = true; BallBtn.Visible = false end)

local function Notify(text, color)
    local notif = Instance.new("TextLabel")
    notif.Size = UDim2.new(0, 280, 0, 40)
    notif.Position = UDim2.new(0.5, -140, 0, 10)
    notif.BackgroundColor3 = color or Color3.fromRGB(30, 30, 40)
    notif.Text = text
    notif.TextColor3 = Color3.fromRGB(255, 255, 255)
    notif.Font = Enum.Font.GothamBold
    notif.TextSize = 13
    notif.Parent = ScreenGui
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 8); uic.Parent = notif
    local uis = Instance.new("UIStroke"); uis.Color = Color3.fromRGB(255, 255, 255); uis.Transparency = 0.5; uis.Parent = notif
    
    notif.Position = UDim2.new(0.5, -140, -0.1, 0)
    TweenService:Create(notif, TweenInfo.new(0.3, Enum.EasingStyle.Back), {Position = UDim2.new(0.5, -140, 0.05, 0)}):Play()
    task.delay(2.5, function()
        TweenService:Create(notif, TweenInfo.new(0.3), {Position = UDim2.new(0.5, -140, -0.1, 0), TextTransparency = 1, BackgroundTransparency = 1}):Play()
        task.wait(0.3)
        notif:Destroy()
    end)
end

local LeftPanel = Instance.new("Frame")
LeftPanel.Size = UDim2.new(0.45, -15, 1, -55)
LeftPanel.Position = UDim2.new(0, 10, 0, 45)
LeftPanel.BackgroundTransparency = 1
LeftPanel.Parent = MainFrame

local RightPanel = Instance.new("ScrollingFrame")
RightPanel.Size = UDim2.new(0.55, -10, 1, -55)
RightPanel.Position = UDim2.new(0.45, 5, 0, 45)
RightPanel.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
RightPanel.BorderSizePixel = 0
RightPanel.CanvasSize = UDim2.new(0, 0, 0, 0)
RightPanel.ScrollBarThickness = 4
RightPanel.ScrollBarImageColor3 = Color3.fromRGB(0, 255, 255)
RightPanel.Parent = MainFrame
local UICornerRight = Instance.new("UICorner"); UICornerRight.CornerRadius = UDim.new(0, 10); UICornerRight.Parent = RightPanel

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Padding = UDim.new(0, 6)
UIListLayout.Parent = RightPanel

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(1, 0, 0, 40)
StatusLabel.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
StatusLabel.Text = "Status: Pronto"
StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 255)
StatusLabel.Font = Enum.Font.GothamBold
StatusLabel.TextSize = 12
StatusLabel.TextWrapped = true
StatusLabel.Parent = LeftPanel
local UICornerStatus = Instance.new("UICorner"); UICornerStatus.CornerRadius = UDim.new(0, 8); UICornerStatus.Parent = StatusLabel

local FilterFrame = Instance.new("ScrollingFrame")
FilterFrame.Size = UDim2.new(1, 0, 0, 180)
FilterFrame.Position = UDim2.new(0, 0, 0, 45)
FilterFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
FilterFrame.BorderSizePixel = 0
FilterFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
FilterFrame.ScrollBarThickness = 3
FilterFrame.ScrollBarImageColor3 = Color3.fromRGB(0, 255, 255)
FilterFrame.Parent = LeftPanel
local UICornerFilter = Instance.new("UICorner"); UICornerFilter.CornerRadius = UDim.new(0, 8); UICornerFilter.Parent = FilterFrame

local FilterLayout = Instance.new("UIListLayout")
FilterLayout.Padding = UDim.new(0, 5)
FilterLayout.Parent = FilterFrame

local function CreateRarityToggle(rarityName, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 28)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    btn.Text = "  ☐ " .. rarityName
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    btn.Parent = FilterFrame
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 6); uic.Parent = btn
    
    local isActive = false
    btn.MouseButton1Click:Connect(function()
        isActive = not isActive
        if isActive then
            btn.BackgroundColor3 = color or Color3.fromRGB(0, 150, 255)
            btn.TextColor3 = Color3.fromRGB(255, 255, 255)
            btn.Text = "  ☑ " .. rarityName
            activeRarities[rarityName] = true
        else
            btn.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
            btn.TextColor3 = Color3.fromRGB(200, 200, 200)
            btn.Text = "  ☐ " .. rarityName
            activeRarities[rarityName] = nil
        end
    end)
end

CreateRarityToggle("Divino", Color3.fromRGB(255, 215, 0))
CreateRarityToggle("Secreto", Color3.fromRGB(50, 50, 50))
CreateRarityToggle("Mítico", Color3.fromRGB(200, 50, 200))
CreateRarityToggle("Lendário", Color3.fromRGB(255, 165, 0))
CreateRarityToggle("Épico", Color3.fromRGB(150, 50, 200))
CreateRarityToggle("Raro", Color3.fromRGB(0, 150, 255))
CreateRarityToggle("Incomum", Color3.fromRGB(0, 255, 0))
CreateRarityToggle("Comum", Color3.fromRGB(150, 150, 150))

FilterLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    FilterFrame.CanvasSize = UDim2.new(0, 0, 0, FilterLayout.AbsoluteContentSize.Y + 10)
end)

local function CreateActionButton(text, yPos, color, clickFunc)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 40)
    btn.Position = UDim2.new(0, 0, 0, yPos)
    btn.BackgroundColor3 = color
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBlack
    btn.TextSize = 13
    btn.Parent = LeftPanel
    local uic = Instance.new("UICorner"); uic.CornerRadius = UDim.new(0, 8); uic.Parent = btn
    
    btn.MouseEnter:Connect(function() TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = color:Lerp(Color3.new(1,1,1), 0.3)}):Play() end)
    btn.MouseLeave:Connect(function() TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = color}):Play() end)
    btn.MouseButton1Click:Connect(clickFunc)
    return btn
end

local ToggleBtn = CreateActionButton("▶ INICIAR AUTO FARM", 235, Color3.fromRGB(0, 200, 100), function()
    if isFarming then
        isFarming = false
        ToggleBtn.Text = "▶ INICIAR AUTO FARM"
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
        StatusLabel.Text = "Status: Parado"
    else
        task.spawn(StartAutoFarm)
    end
end)

local SellBtn = CreateActionButton("💰 VENDER TUDO (SellAll)", 285, Color3.fromRGB(200, 150, 0), function()
    if SellAllRemote then
        StatusLabel.Text = "Vendendo..."
        pcall(function() SellAllRemote:FireServer() end)
        Notify("✅ Exus Vendidos!", Color3.fromRGB(0, 150, 0))
    end
end)

local ScanBtn = CreateActionButton("🔍 ESCANEAR MAPA", 335, Color3.fromRGB(0, 100, 255), function() UpdateExuList() end)

-- ==========================================
-- 3. FUNÇÕES DO JOGO (UNIVERSAL)
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

local function GetRarityColor(rarity)
    local lr = string.lower(rarity)
    if string.find(lr, "divin") then return Color3.fromRGB(255, 215, 0)
    elseif string.find(lr, "secret") or string.find(lr, "secreto") then return Color3.fromRGB(100, 100, 100)
    elseif string.find(lr, "mitic") then return Color3.fromRGB(200, 50, 200)
    elseif string.find(lr, "lend") then return Color3.fromRGB(255, 165, 0)
    elseif string.find(lr, "epic") then return Color3.fromRGB(150, 50, 200)
    elseif string.find(lr, "rare") then return Color3.fromRGB(0, 150, 255)
    elseif string.find(lr, "incom") then return Color3.fromRGB(0, 255, 0)
    else return Color3.fromRGB(150, 150, 150) end
end

local function IsHoldingExu()
    local char = LocalPlayer.Character
    local backpack = LocalPlayer.Backpack
    if char then for _, v in pairs(char:GetChildren()) do if v:IsA("Tool") or string.find(string.lower(v.Name), "exu") then return true end end end
    if backpack then for _, v in pairs(backpack:GetChildren()) do if v:IsA("Tool") or string.find(string.lower(v.Name), "exu") then return true end end end
    return false
end

local function FlyTo(targetCFrame, speed)
    if not hrp then return end
    local distance = (hrp.Position - targetCFrame.Position).Magnitude
    local timeToFly = distance / (speed or 400)
    if timeToFly > 3 then timeToFly = 3 end 
    if timeToFly < 0.1 then timeToFly = 0.1 end
    local tween = TweenService:Create(hrp, TweenInfo.new(timeToFly, Enum.EasingStyle.Linear), {CFrame = targetCFrame})
    tween:Play()
    tween.Completed:Wait()
end

-- A FUNÇÃO DE GRAB UNIVERSAL (PC + MOBILE)
local function StealSpecificExu(model, prompt, exuPart)
    local originalPos = hrp.CFrame
    
    -- 1. Voa até o Exu
    FlyTo(exuPart.CFrame + Vector3.new(0, 3, 0), 400)
    
    -- 2. A MÁGICA PARA MOBILE: Remove a necessidade de segurar o botão
    pcall(function()
        if prompt.HoldDuration and prompt.HoldDuration > 0 then
            prompt.HoldDuration = 0 -- Força o botão a ser um clique rápido
        end
    end)
    
    task.wait(0.1)
    
    -- 3. Tenta pegar de várias formas
    pcall(function() 
        fireproximityprompt(prompt) -- Método universal
        if DropExuRemote then 
            DropExuRemote:FireServer(exuPart)
            DropExuRemote:FireServer(exuPart.Position)
        end
    end)
    
    -- Simula apertar "E" (PC) e toque (Mobile) como backup
    pcall(function()
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.E, false, game)
        task.wait(0.05)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.E, false, game)
    end)

    -- 4. Espera pegar na mão (Lógica do V5)
    local grabbed = false
    local timeout = tick() + 3 
    
    while tick() < timeout do
        if IsHoldingExu() then
            grabbed = true
            break
        end
        task.wait(0.05)
    end
    
    -- 5. Volta pra base
    if grabbed then
        task.wait(0.05)
        hrp.CFrame = originalPos
        task.wait(0.2)
    else
        hrp.CFrame = originalPos
        task.wait(1)
    end
    
    return grabbed
end

-- ==========================================
-- 4. ATUALIZAÇÃO DA LISTA
-- ==========================================
function UpdateExuList()
    for _, child in pairs(RightPanel:GetChildren()) do
        if child:IsA("TextLabel") or child:IsA("Frame") then child:Destroy() end
    end
    
    local count = 0
    local foundRarities = {}
    
    for _, obj in pairs(game.Workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") and string.lower(obj.ActionText) == "roubar" then
            local model = obj.Parent
            if model then
                if model.Parent and not model.Parent:IsA("Workspace") and model.Parent ~= game.Workspace then model = model.Parent end
                local name, rarity = GetExuData(model)
                local exuPart = obj.Parent:IsA("BasePart") and obj.Parent or obj.Parent:FindFirstChildWhichIsA("BasePart")
                
                if exuPart then
                    foundRarities[rarity] = (foundRarities[rarity] or 0) + 1
                    
                    local row = Instance.new("Frame")
                    row.Size = UDim2.new(1, -10, 0, 40)
                    row.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
                    row.Parent = RightPanel
                    local rowCorner = Instance.new("UICorner"); rowCorner.CornerRadius = UDim.new(0, 6); rowCorner.Parent = row
                    
                    local text = Instance.new("TextLabel")
                    text.Size = UDim2.new(1, -70, 1, 0)
                    text.Position = UDim2.new(0, 10, 0, 0)
                    text.BackgroundTransparency = 1
                    text.Text = string.format("%s", name)
                    text.TextColor3 = GetRarityColor(rarity)
                    text.TextXAlignment = Enum.TextXAlignment.Left
                    text.Font = Enum.Font.GothamBold
                    text.TextSize = 13
                    text.Parent = row
                    
                    local rarityLabel = Instance.new("TextLabel")
                    rarityLabel.Size = UDim2.new(1, -70, 1, 0)
                    rarityLabel.Position = UDim2.new(0, 10, 0, 12)
                    rarityLabel.BackgroundTransparency = 1
                    rarityLabel.Text = "⭐ " .. rarity
                    rarityLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
                    rarityLabel.TextXAlignment = Enum.TextXAlignment.Left
                    rarityLabel.Font = Enum.Font.Gotham
                    rarityLabel.TextSize = 10
                    rarityLabel.Parent = row
                    
                    local stealBtn = Instance.new("TextButton")
                    stealBtn.Size = UDim2.new(0, 60, 0, 28)
                    stealBtn.Position = UDim2.new(1, -70, 0.5, -14)
                    stealBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 255)
                    stealBtn.Text = "ROUBAR"
                    stealBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
                    stealBtn.Font = Enum.Font.GothamBold
                    stealBtn.TextSize = 11
                    stealBtn.Parent = row
                    local btnCorner = Instance.new("UICorner"); btnCorner.CornerRadius = UDim.new(0, 6); btnCorner.Parent = stealBtn
                    
                    stealBtn.MouseButton1Click:Connect(function()
                        if not isFarming then
                            stealBtn.Text = "..."
                            stealBtn.BackgroundColor3 = Color3.fromRGB(255, 150, 0)
                            local success = StealSpecificExu(model, obj, exuPart)
                            if success then
                                Notify("✅ " .. name .. " Roubado!", Color3.fromRGB(0, 150, 0))
                                stealBtn.Text = "OK!"
                                stealBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
                                if PlaceExuRemote then pcall(function() PlaceExuRemote:FireServer() end) end
                            else
                                Notify("❌ Falha ao pegar.", Color3.fromRGB(150, 0, 0))
                                stealBtn.Text = "ERRO"
                                stealBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
                            end
                            task.wait(1)
                            UpdateExuList()
                        end
                    end)
                    count = count + 1
                end
            end
        end
    end
    
    local summaryText = "Total: " .. count .. " | "
    for r, c in pairs(foundRarities) do summaryText = summaryText .. r .. ": " .. c .. " | " end
    StatusLabel.Text = "Escaneado! " .. summaryText
    
    UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        RightPanel.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 15)
    end)
end

-- ==========================================
-- 5. LOOP DO AUTO FARM
-- ==========================================
function StartAutoFarm()
    if isFarming then return end
    isFarming = true
    ToggleBtn.Text = "⏹ PARAR AUTO FARM"
    ToggleBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
    
    baseCFrame = hrp.CFrame
    StatusLabel.Text = "Base salva! Iniciando V16..."
    
    while isFarming do
        local successLoop, errorMsg = pcall(function()
            local function FindNearest()
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
                return closestPrompt, closestPart
            end

            local prompt, exuPart = FindNearest()
            if prompt and exuPart then
                StatusLabel.Text = "Voando até o Exu..."
                local grabbed = StealSpecificExu(exuPart.Parent, prompt, exuPart)
                
                if grabbed then
                    StatusLabel.Text = "✅ PEGOU! Colocando na base..."
                    hrp.CFrame = baseCFrame + Vector3.new(0, 3, 0)
                    task.wait(0.2)
                    
                    if PlaceExuRemote then
                        pcall(function() PlaceExuRemote:FireServer(baseCFrame.Position); PlaceExuRemote:FireServer() end)
                    end
                    
                    local basePrompt = nil
                    for _, obj in pairs(game.Workspace:GetDescendants()) do
                        if obj:IsA("ProximityPrompt") then
                            local action = string.lower(obj.ActionText)
                            if action == "colocar" or action == "place" or action == "dropar" then
                                if obj.Parent and obj.Parent:IsA("BasePart") then
                                    if (obj.Parent.Position - baseCFrame.Position).Magnitude < 25 then basePrompt = obj; break end
                                end
                            end
                        end
                    end
                    if basePrompt then pcall(function() fireproximityprompt(basePrompt) end) end
                    
                    task.wait(0.5)
                else
                    StatusLabel.Text = "❌ Falha ao pegar."
                    hrp.CFrame = baseCFrame + Vector3.new(0, 3, 0)
                    task.wait(1)
                end
            else
                StatusLabel.Text = "Nenhum Exu encontrado."
                task.wait(1)
            end
        end)
        
        if not successLoop then
            StatusLabel.Text = "Erro detectado, reiniciando loop..."
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
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
        StatusLabel.Text = "Você morreu! Farm parado."
    end
end)

task.wait(1)
UpdateExuList()
Notify("👻 V16 Universal Carregado!", Color3.fromRGB(0, 100, 255))
