-- ========================================
-- DRIFT DE RE BY DVZX (GUI ONLY - NADA FUNCIONA)
-- Segura angulo + desliza mais
-- ========================================
local Players = game:GetService("Players")
local RS = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local LP = Players.LocalPlayer
local PG = LP:WaitForChild("PlayerGui")
local on, rev = false, false
local maxR, minR = 110, 35
local dPow, dSpin = 0.26, 3.1
local angBoost = 2.0
local smoothness = 0.5
local root, seat, conn
local sSteer, sYaw = 0, 0
local slideKeep = 0
local gearT, lastG = {}, 0
local page = 1
local minimized = false
local waitingBind = nil
local bindAtivar = Enum.KeyCode.E
local bindCravar = Enum.KeyCode.R

local function KeyName(c)
    return tostring(c):gsub("Enum.KeyCode.", "")
end

-- ===== TODAS AS FUNÇÕES DE LÓGICA DESATIVADAS =====
local function GetCarParts()
    return nil, nil
end

local function GetSteerInput()
    return 0
end

local function CacheGear()
end

local function ForceR()
end

local function Step(dt)
    -- nada acontece
end

-- ==================== GUI ====================
pcall(function()
    local old = PG:FindFirstChild("DriftReDvzx")
    if old then old:Destroy() end
end)

local Gui = Instance.new("ScreenGui")
Gui.Name = "DriftReDvzx"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.DisplayOrder = 100
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Gui.Parent = PG

local Main = Instance.new("Frame")
Main.Size = UDim2.fromOffset(280, 360)
Main.Position = UDim2.new(0, 16, 0.25, 0)
Main.BackgroundColor3 = Color3.fromRGB(18, 20, 28)
Main.BorderSizePixel = 0
Main.Active = true
Main.Parent = Gui
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 12)

local border = Instance.new("UIStroke")
border.Color = Color3.fromRGB(90, 140, 255)
border.Thickness = 1.2
border.Transparency = 0.4
border.Parent = Main

local Top = Instance.new("Frame")
Top.Size = UDim2.new(1, 0, 0, 40)
Top.BackgroundColor3 = Color3.fromRGB(30, 36, 52)
Top.BorderSizePixel = 0
Top.ZIndex = 5
Top.Parent = Main
Instance.new("UICorner", Top).CornerRadius = UDim.new(0, 12)

local TopBottom = Instance.new("Frame")
TopBottom.Size = UDim2.new(1, 0, 0, 12)
TopBottom.Position = UDim2.new(0, 0, 1, -12)
TopBottom.BackgroundColor3 = Color3.fromRGB(30, 36, 52)
TopBottom.BorderSizePixel = 0
TopBottom.ZIndex = 5
TopBottom.Parent = Top

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -110, 1, 0)
Title.Position = UDim2.fromOffset(12, 0)
Title.BackgroundTransparency = 1
Title.Text = "drift de re by dvzx"
Title.TextColor3 = Color3.fromRGB(150, 190, 255)
Title.TextSize = 13
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 6
Title.Parent = Top

local function MakeTopButton(name, text, offset, bg, color)
    local b = Instance.new("TextButton")
    b.Name = name
    b.Size = UDim2.fromOffset(26, 26)
    b.Position = UDim2.new(1, offset, 0.5, -13)
    b.BackgroundColor3 = bg
    b.BorderSizePixel = 0
    b.Text = text
    b.TextColor3 = color
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.ZIndex = 10
    b.Active = true
    b.Parent = Top
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
    return b
end

local BtnCfg = MakeTopButton("Cfg", "CFG", -94, Color3.fromRGB(45, 60, 95), Color3.fromRGB(200, 220, 255))
local BtnMin = MakeTopButton("Min", "-", -62, Color3.fromRGB(45, 55, 75), Color3.fromRGB(220, 220, 230))
local BtnClose = MakeTopButton("Close", "X", -30, Color3.fromRGB(90, 40, 50), Color3.fromRGB(255, 140, 150))

local Status = Instance.new("TextLabel")
Status.Size = UDim2.new(1, -20, 0, 22)
Status.Position = UDim2.fromOffset(10, 48)
Status.BackgroundColor3 = Color3.fromRGB(26, 30, 42)
Status.BorderSizePixel = 0
Status.Text = "  desliza e segura angulo"
Status.TextColor3 = Color3.fromRGB(170, 185, 210)
Status.TextSize = 11
Status.Font = Enum.Font.Gotham
Status.TextXAlignment = Enum.TextXAlignment.Left
Status.ZIndex = 3
Status.Parent = Main
Instance.new("UICorner", Status).CornerRadius = UDim.new(0, 6)

local function SetStatus(t) Status.Text = "  " .. t end

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -16, 1, -80)
Content.Position = UDim2.fromOffset(8, 76)
Content.BackgroundTransparency = 1
Content.ZIndex = 3
Content.Parent = Main

local PageMain = Instance.new("Frame")
PageMain.Size = UDim2.fromScale(1, 1)
PageMain.BackgroundTransparency = 1
PageMain.ZIndex = 4
PageMain.Parent = Content

local function MakeMainBtn(text, y, h, color)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, h or 44)
    b.Position = UDim2.fromOffset(0, y)
    b.BackgroundColor3 = color
    b.BorderSizePixel = 0
    b.Text = text
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 15
    b.Font = Enum.Font.GothamBold
    b.ZIndex = 5
    b.Active = true
    b.Parent = PageMain
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 10)
    return b
end

local BtnAtivar = MakeMainBtn("ATIVAR", 0, 48, Color3.fromRGB(40, 130, 80))
local BtnCravar = MakeMainBtn("CRAVAR RE", 56, 48, Color3.fromRGB(170, 100, 40))
local BtnParar = MakeMainBtn("PARAR / DESATIVAR", 112, 44, Color3.fromRGB(130, 45, 55))

local Tip = Instance.new("TextLabel")
Tip.Size = UDim2.new(1, 0, 0, 50)
Tip.Position = UDim2.fromOffset(0, 168)
Tip.BackgroundTransparency = 1
Tip.Text = "segura o angulo e desliza mais\nmobile: botoes flutuantes"
Tip.TextColor3 = Color3.fromRGB(120, 135, 160)
Tip.TextSize = 11
Tip.Font = Enum.Font.Gotham
Tip.ZIndex = 4
Tip.Parent = PageMain

local PageCfg = Instance.new("ScrollingFrame")
PageCfg.Size = UDim2.fromScale(1, 1)
PageCfg.BackgroundTransparency = 1
PageCfg.BorderSizePixel = 0
PageCfg.ScrollBarThickness = 4
PageCfg.ScrollBarImageColor3 = Color3.fromRGB(90, 140, 255)
PageCfg.CanvasSize = UDim2.fromOffset(0, 440)
PageCfg.Visible = false
PageCfg.ZIndex = 4
PageCfg.Parent = Content

local list = Instance.new("UIListLayout")
list.Padding = UDim.new(0, 6)
list.SortOrder = Enum.SortOrder.LayoutOrder
list.Parent = PageCfg

local function CfgText(text, order)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, -6, 0, 16)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = Color3.fromRGB(150, 170, 200)
    l.TextSize = 11
    l.Font = Enum.Font.GothamBold
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.LayoutOrder = order
    l.ZIndex = 5
    l.Parent = PageCfg
    return l
end

local function CfgButton(text, color, order)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -6, 0, 34)
    b.BackgroundColor3 = color
    b.BorderSizePixel = 0
    b.Text = text
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.LayoutOrder = order
    b.ZIndex = 5
    b.Active = true
    b.Parent = PageCfg
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
    return b
end

local function CfgSlider(order, title, color, defaultText)
    local box = Instance.new("Frame")
    box.Size = UDim2.new(1, -6, 0, 42)
    box.BackgroundTransparency = 1
    box.LayoutOrder = order
    box.ZIndex = 5
    box.Parent = PageCfg

    local lab = Instance.new("TextLabel")
    lab.Size = UDim2.new(1, 0, 0, 14)
    lab.BackgroundTransparency = 1
    lab.Text = title
    lab.TextColor3 = Color3.fromRGB(190, 205, 230)
    lab.TextSize = 11
    lab.Font = Enum.Font.GothamBold
    lab.TextXAlignment = Enum.TextXAlignment.Left
    lab.ZIndex = 5
    lab.Parent = box

    local bg = Instance.new("Frame")
    bg.Size = UDim2.new(1, 0, 0, 22)
    bg.Position = UDim2.fromOffset(0, 16)
    bg.BackgroundColor3 = Color3.fromRGB(32, 38, 54)
    bg.BorderSizePixel = 0
    bg.ZIndex = 5
    bg.Parent = box
    Instance.new("UICorner", bg).CornerRadius = UDim.new(0, 7)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new(0.35, 0, 1, 0)
    fill.BackgroundColor3 = color
    fill.BorderSizePixel = 0
    fill.ZIndex = 6
    fill.Parent = bg
    Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 7)

    local val = Instance.new("TextLabel")
    val.Size = UDim2.fromScale(1, 1)
    val.BackgroundTransparency = 1
    val.Text = defaultText
    val.TextColor3 = Color3.fromRGB(240, 245, 255)
    val.TextSize = 11
    val.Font = Enum.Font.GothamBold
    val.ZIndex = 7
    val.Parent = bg

    local hit = Instance.new("TextButton")
    hit.Size = UDim2.fromScale(1, 1)
    hit.BackgroundTransparency = 1
    hit.Text = ""
    hit.ZIndex = 8
    hit.Active = true
    hit.Parent = bg

    return bg, fill, val, hit
end

local function BindSlider(bg, fill, hit, cb)
    local dragging = false
    local function upd(x)
        local rel = math.clamp((x - bg.AbsolutePosition.X) / math.max(bg.AbsoluteSize.X, 1), 0, 1)
        fill.Size = UDim2.new(rel, 0, 1, 0)
        cb(rel)
    end
    hit.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            upd(input.Position.X)
        end
    end)
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    UIS.InputChanged:Connect(function(input)
        if not dragging then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            upd(input.Position.X)
        end
    end)
end

CfgText("configuracao", 1)
local BindAtivarInfo = CfgText("ativar: E", 2)
local BtnBindAtivar = CfgButton("KEYBIND ATIVAR (PC)", Color3.fromRGB(55, 90, 150), 3)
local BindCravarInfo = CfgText("cravar re: R", 4)
local BtnBindCravar = CfgButton("KEYBIND CRAVAR RE (PC)", Color3.fromRGB(55, 90, 150), 5)

local bgMin, fillMin, valMin, hitMin = CfgSlider(6, "RE MINIMA", Color3.fromRGB(70, 180, 120), "min: 35")
local bgMax, fillMax, valMax, hitMax = CfgSlider(7, "RE MAXIMA", Color3.fromRGB(80, 140, 255), "max: 110")
local bgDr, fillDr, valDr, hitDr = CfgSlider(8, "FORCA DRIFT", Color3.fromRGB(220, 140, 60), "drift: 0.26")
local bgSp, fillSp, valSp, hitSp = CfgSlider(9, "GIRO / ANGULO", Color3.fromRGB(200, 100, 180), "giro: 3.1")
local bgBoost, fillBoost, valBoost, hitBoost = CfgSlider(10, "BOOST ANGULO", Color3.fromRGB(255, 120, 100), "boost: 2.00")
local BtnVoltar = CfgButton("VOLTAR", Color3.fromRGB(50, 56, 72), 11)

BindSlider(bgMin, fillMin, hitMin, function(r)
    minR = math.floor(r * 200 / 5) * 5
    valMin.Text = "min: " .. minR
end)
BindSlider(bgMax, fillMax, hitMax, function(r)
    maxR = math.floor((40 + r * 360) / 10) * 10
    valMax.Text = "max: " .. maxR
end)
BindSlider(bgDr, fillDr, hitDr, function(r)
    dPow = 0.08 + r * 0.4
    valDr.Text = string.format("drift: %.2f", dPow)
end)
BindSlider(bgSp, fillSp, hitSp, function(r)
    dSpin = 1.0 + r * 4.2
    valSp.Text = string.format("giro: %.1f", dSpin)
end)
BindSlider(bgBoost, fillBoost, hitBoost, function(r)
    angBoost = 1.0 + r * 1.9
    valBoost.Text = string.format("boost: %.2f", angBoost)
end)

local MobileBar = Instance.new("Frame")
MobileBar.Size = UDim2.fromOffset(200, 96)
MobileBar.Position = UDim2.new(1, -210, 1, -180)
MobileBar.BackgroundColor3 = Color3.fromRGB(18, 20, 28)
MobileBar.BackgroundTransparency = 0.15
MobileBar.BorderSizePixel = 0
MobileBar.ZIndex = 20
MobileBar.Parent = Gui
Instance.new("UICorner", MobileBar).CornerRadius = UDim.new(0, 12)
Instance.new("UIStroke", MobileBar).Color = Color3.fromRGB(90, 140, 255)

local function MakeMobileBtn(text, y, color)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -12, 0, 38)
    b.Position = UDim2.fromOffset(6, y)
    b.BackgroundColor3 = color
    b.BorderSizePixel = 0
    b.Text = text
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 13
    b.Font = Enum.Font.GothamBold
    b.ZIndex = 21
    b.Active = true
    b.Parent = MobileBar
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 9)
    return b
end

local MobAtivar = MakeMobileBtn("ATIVAR", 8, Color3.fromRGB(40, 130, 80))
local MobCravar = MakeMobileBtn("CRAVAR RE", 50, Color3.fromRGB(170, 100, 40))

local function ShowPage(n)
    page = n
    PageMain.Visible = (n == 1) and not minimized
    PageCfg.Visible = (n == 2) and not minimized
    waitingBind = nil
    BtnBindAtivar.Text = "KEYBIND ATIVAR (PC)"
    BtnBindCravar.Text = "KEYBIND CRAVAR RE (PC)"
    if not minimized then
        Main.Size = (n == 2) and UDim2.fromOffset(280, 420) or UDim2.fromOffset(280, 360)
    end
end

local function UpdateBinds()
    BindAtivarInfo.Text = "ativar: " .. KeyName(bindAtivar)
    BindCravarInfo.Text = "cravar re: " .. KeyName(bindCravar)
    SetStatus(KeyName(bindAtivar) .. " = ativar | " .. KeyName(bindCravar) .. " = re")
end

-- ===== FUNÇÕES DE AÇÃO DESATIVADAS (só mudam texto visual) =====
local function StopAll()
    on, rev = false, false
    slideKeep = 0
    if conn then conn:Disconnect() conn = nil end
    BtnAtivar.Text = "ATIVAR"
    BtnAtivar.BackgroundColor3 = Color3.fromRGB(40, 130, 80)
    MobAtivar.Text = "ATIVAR"
    MobAtivar.BackgroundColor3 = Color3.fromRGB(40, 130, 80)
    BtnCravar.Text = "CRAVAR RE"
    BtnCravar.BackgroundColor3 = Color3.fromRGB(170, 100, 40)
    MobCravar.Text = "CRAVAR RE"
    MobCravar.BackgroundColor3 = Color3.fromRGB(170, 100, 40)
    UpdateBinds()
end

local function SetReverse(v)
    rev = v
    if rev then
        BtnCravar.Text = "RE ON (sair)"
        BtnCravar.BackgroundColor3 = Color3.fromRGB(210, 130, 50)
        MobCravar.Text = "RE ON"
        MobCravar.BackgroundColor3 = Color3.fromRGB(210, 130, 50)
        SetStatus("re on | desliza mais")
    else
        BtnCravar.Text = "CRAVAR RE"
        BtnCravar.BackgroundColor3 = Color3.fromRGB(170, 100, 40)
        MobCravar.Text = "CRAVAR RE"
        MobCravar.BackgroundColor3 = Color3.fromRGB(170, 100, 40)
        slideKeep = 0
        SetStatus("re off")
    end
end

local function DoAtivar()
    on = not on
    if on then
        BtnAtivar.Text = "DESATIVAR"
        BtnAtivar.BackgroundColor3 = Color3.fromRGB(160, 55, 65)
        MobAtivar.Text = "DESATIVAR"
        MobAtivar.BackgroundColor3 = Color3.fromRGB(160, 55, 65)
        SetStatus("ativo (só visual)")
    else
        StopAll()
    end
end

local function DoCravar()
    if not on then SetStatus("ative primeiro") return end
    SetReverse(not rev)
end

-- Conexões dos botões (só visual)
BtnAtivar.MouseButton1Click:Connect(DoAtivar)
BtnCravar.MouseButton1Click:Connect(DoCravar)
BtnParar.MouseButton1Click:Connect(StopAll)
MobAtivar.MouseButton1Click:Connect(DoAtivar)
MobCravar.MouseButton1Click:Connect(DoCravar)

BtnCfg.MouseButton1Click:Connect(function()
    if minimized then return end
    ShowPage(page == 1 and 2 or 1)
end)

BtnVoltar.MouseButton1Click:Connect(function() ShowPage(1) end)

BtnMin.MouseButton1Click:Connect(function()
    minimized = not minimized
    if minimized then
        Content.Visible = false
        Status.Visible = false
        Main.Size = UDim2.fromOffset(280, 40)
        BtnMin.Text = "+"
    else
        Content.Visible = true
        Status.Visible = true
        BtnMin.Text = "-"
        ShowPage(page)
    end
end)

BtnClose.MouseButton1Click:Connect(function()
    StopAll()
    Gui:Destroy()
end)

BtnBindAtivar.MouseButton1Click:Connect(function()
    waitingBind = "ativar"
    BtnBindAtivar.Text = "APERTE UMA TECLA..."
end)

BtnBindCravar.MouseButton1Click:Connect(function()
    waitingBind = "cravar"
    BtnBindCravar.Text = "APERTE UMA TECLA..."
end)

UIS.InputBegan:Connect(function(input, gp)
    if input.UserInputType ~= Enum.UserInputType.Keyboard then return end
    if input.KeyCode == Enum.KeyCode.Unknown then return end
    if waitingBind then
        if waitingBind == "ativar" then bindAtivar = input.KeyCode
        else bindCravar = input.KeyCode end
        waitingBind = nil
        BtnBindAtivar.Text = "KEYBIND ATIVAR (PC)"
        BtnBindCravar.Text = "KEYBIND CRAVAR RE (PC)"
        UpdateBinds()
        return
    end
    if gp then return end
    if input.KeyCode == bindAtivar then DoAtivar()
    elseif input.KeyCode == bindCravar then DoCravar() end
end)

-- Arrastar GUI (mantido)
local dragging, dragStart, startPos = false, nil, nil
Top.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)
UIS.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local d = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
    end
end)

local mDrag, m0, mp0 = false, nil, nil
MobileBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        mDrag, m0, mp0 = true, input.Position, MobileBar.Position
    end
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        mDrag = false
    end
end)
UIS.InputChanged:Connect(function(input)
    if mDrag and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local d = input.Position - m0
        MobileBar.Position = UDim2.new(mp0.X.Scale, mp0.X.Offset + d.X, mp0.Y.Scale, mp0.Y.Offset + d.Y)
    end
end)

UpdateBinds()
print("✅ GUI carregada (nenhuma função de drift/re funciona)")
