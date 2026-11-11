-- ==================== MOMO HUB V30 (MERGED + NEON WEAPONS) ====================
-- Theme: Violet #8B5CF6 -> Cyan #06B6D4 | BG #111827

local P = game:GetService("Players")
local R = game:GetService("RunService")
local W = game:GetService("Workspace")
local L = game:GetService("Lighting")
local U = game:GetService("UserInputService")
local T = game:GetService("TweenService")
local LP = P.LocalPlayer

local C = {
    Aim = true, SAim = false, ESP = true, Skeleton = true, Tracers = true, POV = true,
    Kaura = false, Noclip = false, Fly = false, Speed = false, Bhop = true,
    God = true, Rapid = true, AutoReload = true, Fullbright = true, FOV = 250,
    SpeedVal = 60, FlyVal = 100, POVSize = 150,
    ESPColor = Color3.fromRGB(255, 0, 0),
    TracersColor = Color3.fromRGB(255, 0, 0),
    POVColor = Color3.fromRGB(255, 255, 255),
    Rainbow = false,
    Crosshair = false,
    CrosshairType = "Dot",
    CrosshairColor = Color3.fromRGB(255, 255, 255),
    CrosshairSize = 5,
    FOVCircle2 = false,
    FOVCircle2Size = 150,
    FOVCircle2Color = Color3.fromRGB(139, 92, 246),
    TimeChanger = false,
    TimeVal = 14,
    FogRemover = false,
    AmbientCustom = false,
    AmbientColor = Color3.fromRGB(135, 206, 235),
    MuteFootsteps = false,
    HitSound = false,
    HitSoundId = "rbxassetid://4612375927",
    AimSmooth = 0.1,
    AimBone = "Head",
    AimPrediction = 0,
    UITransparency = 0.3,
    WatermarkEnabled = true,
    TriggerBot = false,
    HitboxExpand = false,
    HitboxSize = 5,
    HealthESP = true,
    NameESP = true,
    BoxESP = false,
    Radar = false,
    Chams = false,
    ChamsColor = Color3.fromRGB(255, 0, 0),
    InfJump = false,
    KnifeBot = false,
    KnifeBotRange = 10,
    CoinESP = false,
    AutoRespawn = false,
    AntiAFK = false,
    NeonWeapons = false,
    NeonGlow = true,
    NeonHueSpeed = 0.6,
    KB = {
        Fly = Enum.KeyCode.F,
        Speed = Enum.KeyCode.G,
        Noclip = Enum.KeyCode.N,
        Aimbot = Enum.KeyCode.H,
    }
}

pcall(function() setfpscap(999) end)

local function isEnemy(v)
    if v == LP then return false end
    return v.Team == nil or LP.Team == nil or v.Team ~= LP.Team
end

local function getBonePart(char, bone)
    if bone == "Head" then return char:FindFirstChild("Head")
    elseif bone == "Torso" then return char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Torso")
    else return char:FindFirstChild("HumanoidRootPart") end
end

local function getTarget()
    local closest, dist = nil, C.FOV
    local center = Vector2.new(W.CurrentCamera.ViewportSize.X/2, W.CurrentCamera.ViewportSize.Y/2)
    for _, v in pairs(P:GetPlayers()) do
        if v ~= LP and isEnemy(v) and v.Character then
            local bone = getBonePart(v.Character, C.AimBone)
            if bone then
                local sp, os = W.CurrentCamera:WorldToViewportPoint(bone.Position)
                if os and (Vector2.new(sp.X, sp.Y) - center).Magnitude < dist then
                    dist = (Vector2.new(sp.X, sp.Y) - center).Magnitude
                    closest = v
                end
            end
        end
    end
    return closest
end

local oldNamecall
oldNamecall = hookmetamethod(game, "__namecall", function(self, ...)
    local m = getnamecallmethod()
    if m == "Kick" or m == "Ban" then return nil end
    if (m == "FireServer" or m == "InvokeServer") and C.SAim then
        local args = {...}
        local t = getTarget()
        if t then
            for i, v in pairs(args) do
                if typeof(v) == "CFrame" then args[i] = CFrame.new(W.CurrentCamera.CFrame.Position, t.Character.Head.Position)
                elseif typeof(v) == "Vector3" then args[i] = t.Character.Head.Position end
            end
            return oldNamecall(self, unpack(args))
        end
    end
    return oldNamecall(self, ...)
end)

if game.CoreGui:FindFirstChild("MomoHub") then game.CoreGui.MomoHub:Destroy() end
if LP:WaitForChild("PlayerGui"):FindFirstChild("MomoHub") then LP.PlayerGui.MomoHub:Destroy() end
local SG = Instance.new("ScreenGui", LP:WaitForChild("PlayerGui"))
SG.Name = "MomoHub"
SG.ResetOnSpawn = false
SG.IgnoreGuiInset = true

-- THEME
local Theme    = Color3.fromRGB(17, 24, 39)
local Accent   = Color3.fromRGB(139, 92, 246)
local Accent2  = Color3.fromRGB(6, 182, 212)
local BgDark   = Color3.fromRGB(17, 24, 39)
local DarkGrey = Color3.fromRGB(31, 41, 55)
local OnText   = Color3.fromRGB(255, 255, 255)

-- Logo M (replika SVG pakai Frame)
local function createLogo(parent, zIndex)
    zIndex = zIndex or 1
    local bg = Instance.new("Frame")
    bg.Name = "Logo"
    bg.Size = UDim2.new(1, 0, 1, 0)
    bg.BackgroundColor3 = BgDark
    bg.BorderSizePixel = 0
    bg.ZIndex = zIndex
    bg.Parent = parent
    Instance.new("UICorner", bg).CornerRadius = UDim.new(0.2, 0)

    local thick = 18
    local function seg(ax, ay, bx, by)
        local dx, dy = bx - ax, by - ay
        local len = math.sqrt(dx*dx + dy*dy)
        local f = Instance.new("Frame")
        f.AnchorPoint = Vector2.new(0.5, 0.5)
        f.Position = UDim2.new((ax + bx) / 400, 0, (ay + by) / 400, 0)
        f.Size = UDim2.new(thick / 200, 0, (len + thick) / 200, 0)
        f.Rotation = math.deg(math.atan2(-dx, dy))
        f.BackgroundColor3 = Color3.new(1, 1, 1)
        f.BorderSizePixel = 0
        f.ZIndex = zIndex + 1
        f.Parent = bg
        Instance.new("UICorner", f).CornerRadius = UDim.new(0.5, 0)
        local g = Instance.new("UIGradient", f)
        g.Rotation = 90
        g.Color = ColorSequence.new(
            Accent:Lerp(Accent2, math.clamp((ax + ay) / 400, 0, 1)),
            Accent:Lerp(Accent2, math.clamp((bx + by) / 400, 0, 1))
        )
    end

    seg(45, 140, 45, 60)
    seg(55, 50, 100, 100)
    seg(100, 100, 145, 50)
    seg(155, 60, 155, 140)
    return bg
end

-- Drawings
local POV = Drawing.new("Circle")
POV.Thickness = 1
POV.Filled = false
POV.Visible = true

local POV2 = Drawing.new("Circle")
POV2.Thickness = 1.5
POV2.Filled = false
POV2.Visible = false

local CrossDot = Drawing.new("Circle")
CrossDot.Filled = true
CrossDot.Radius = 3
CrossDot.Visible = false

local CrossLines = {}
for i = 1, 4 do
    CrossLines[i] = Drawing.new("Line")
    CrossLines[i].Thickness = 1.5
    CrossLines[i].Visible = false
end

-- Watermark
local WMLabel = Instance.new("TextLabel", SG)
WMLabel.Size = UDim2.new(0, 300, 0, 24)
WMLabel.Position = UDim2.new(0, 10, 0, 10)
WMLabel.BackgroundColor3 = BgDark
WMLabel.BackgroundTransparency = 0.3
WMLabel.TextColor3 = Accent2
WMLabel.Font = Enum.Font.GothamBold
WMLabel.TextSize = 12
WMLabel.Text = "MOMO HUB V30"
WMLabel.TextXAlignment = Enum.TextXAlignment.Left
Instance.new("UICorner", WMLabel).CornerRadius = UDim.new(0, 4)
local WMPad = Instance.new("UIPadding", WMLabel)
WMPad.PaddingLeft = UDim.new(0, 8)
WMLabel.Visible = C.WatermarkEnabled

-- Hit Sound
local HitSFX = Instance.new("Sound", SG)
HitSFX.SoundId = "rbxassetid://4612375927"
HitSFX.Volume = 1

-- ESP Setup
local espF = Instance.new("Folder", SG)
espF.Name = "ESP_Highlights"
local D = {}

local function initEsp(v)
    if v == LP then return end
    local line = Drawing.new("Line")
    line.Visible = false
    line.Thickness = 2
    local skeletonLines = {}
    for i = 1, 15 do
        local sLine = Drawing.new("Line")
        sLine.Thickness = 1.5
        sLine.Visible = false
        skeletonLines[i] = sLine
    end
    local hl = Instance.new("Highlight", espF)
    hl.FillTransparency = 0.6
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    D[v.Name] = {line = line, skeleton = skeletonLines, hl = hl}
    v.CharacterAdded:Connect(function(c) hl.Adornee = c end)
    if v.Character then hl.Adornee = v.Character end
end

for _, v in pairs(P:GetPlayers()) do initEsp(v) end
P.PlayerAdded:Connect(initEsp)
P.PlayerRemoving:Connect(function(v)
    local d = D[v.Name]
    if d then
        d.line:Remove()
        for _, l in pairs(d.skeleton) do l:Remove() end
        d.hl:Destroy()
        if d.hbBg then d.hbBg:Remove() end
        if d.hbFill then d.hbFill:Remove() end
        if d.nameDraw then d.nameDraw:Remove() end
        if d.box then for _, b in pairs(d.box) do b:Remove() end end
        D[v.Name] = nil
    end
end)

-- RADAR UI (Draggable)
local RadarSize = 180
local RadarFrame = Instance.new("Frame", SG)
RadarFrame.Size = UDim2.new(0, RadarSize, 0, RadarSize)
RadarFrame.Position = UDim2.new(1, -RadarSize - 16, 0, 16)
RadarFrame.BackgroundColor3 = Color3.fromRGB(10, 14, 24)
RadarFrame.BackgroundTransparency = 0.15
RadarFrame.BorderSizePixel = 0
RadarFrame.Visible = false
RadarFrame.ClipsDescendants = true
Instance.new("UICorner", RadarFrame).CornerRadius = UDim.new(1, 0)
local RadarStroke = Instance.new("UIStroke", RadarFrame)
RadarStroke.Color = Accent
RadarStroke.Thickness = 1.5
RadarStroke.Transparency = 0.3

local function makeRadarLine(parent, x1, y1, x2, y2, transp)
    local l = Instance.new("Frame", parent)
    l.BackgroundColor3 = Accent
    l.BackgroundTransparency = transp or 0.7
    l.BorderSizePixel = 0
    if x1 == x2 then
        l.Position = UDim2.new(x1, 0, 0, 0)
        l.Size = UDim2.new(0, 1, 1, 0)
    else
        l.Position = UDim2.new(0, 0, y1, 0)
        l.Size = UDim2.new(1, 0, 0, 1)
    end
end
makeRadarLine(RadarFrame, 0.5, 0, 0.5, 1, 0.75)
makeRadarLine(RadarFrame, 0, 0.5, 1, 0.5, 0.75)

local RadarLabel = Instance.new("TextLabel", RadarFrame)
RadarLabel.Size = UDim2.new(1, 0, 0, 16)
RadarLabel.Position = UDim2.new(0, 0, 0, 2)
RadarLabel.BackgroundTransparency = 1
RadarLabel.Text = "RADAR"
RadarLabel.TextColor3 = Accent2
RadarLabel.Font = Enum.Font.GothamBold
RadarLabel.TextSize = 10

local SelfDot = Instance.new("Frame", RadarFrame)
SelfDot.Size = UDim2.new(0, 6, 0, 6)
SelfDot.Position = UDim2.new(0.5, -3, 0.5, -3)
SelfDot.BackgroundColor3 = Accent2
SelfDot.BorderSizePixel = 0
Instance.new("UICorner", SelfDot).CornerRadius = UDim.new(1, 0)
SelfDot.ZIndex = 3

local radarDotPool = {}
for i = 1, 20 do
    local dot = Instance.new("Frame", RadarFrame)
    dot.Size = UDim2.new(0, 6, 0, 6)
    dot.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    dot.BorderSizePixel = 0
    dot.Visible = false
    dot.ZIndex = 4
    Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    radarDotPool[i] = dot
end

local radarDragging, radarDragStart, radarStartPos
RadarFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        radarDragging = true
        radarDragStart = input.Position
        radarStartPos = RadarFrame.Position
    end
end)
U.InputChanged:Connect(function(input)
    if radarDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - radarDragStart
        RadarFrame.Position = UDim2.new(
            radarStartPos.X.Scale, radarStartPos.X.Offset + delta.X,
            radarStartPos.Y.Scale, radarStartPos.Y.Offset + delta.Y
        )
    end
end)
U.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        radarDragging = false
    end
end)

-- ESP DRAWING OBJECTS
local function initEspExtras(v)
    if v == LP or D[v.Name] == nil then return end
    local hbBg = Drawing.new("Line")
    hbBg.Thickness = 4
    hbBg.Color = Color3.fromRGB(50, 50, 50)
    hbBg.Visible = false
    local hbFill = Drawing.new("Line")
    hbFill.Thickness = 4
    hbFill.Color = Color3.fromRGB(0, 255, 100)
    hbFill.Visible = false
    local nameDraw = Drawing.new("Text")
    nameDraw.Size = 13
    nameDraw.Font = Drawing.Fonts.UI
    nameDraw.Color = Color3.fromRGB(255, 255, 255)
    nameDraw.Outline = true
    nameDraw.OutlineColor = Color3.fromRGB(0, 0, 0)
    nameDraw.Visible = false
    local box = {}
    for i = 1, 4 do
        local bl = Drawing.new("Line")
        bl.Thickness = 1.5
        bl.Color = Color3.fromRGB(255, 0, 0)
        bl.Visible = false
        box[i] = bl
    end
    D[v.Name].hbBg = hbBg
    D[v.Name].hbFill = hbFill
    D[v.Name].nameDraw = nameDraw
    D[v.Name].box = box
end

for _, v in pairs(P:GetPlayers()) do initEspExtras(v) end
P.PlayerAdded:Connect(function(v)
    task.wait(0.1)
    initEspExtras(v)
end)   

local Main = Instance.new("Frame", SG)
Main.Size = UDim2.new(0, 540, 0, 390)
Main.Position = UDim2.new(0.5, -270, 0.5, -195)
Main.BackgroundColor3 = BgDark
Main.BorderSizePixel = 0
Main.ClipsDescendants = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 14)
local MainStroke = Instance.new("UIStroke", Main)
MainStroke.Color = Accent
MainStroke.Thickness = 1.5
MainStroke.Transparency = 0.3
Main.BackgroundTransparency = C.UITransparency

local MainGrad = Instance.new("UIGradient", Main)
MainGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(31, 41, 55)),
    ColorSequenceKeypoint.new(1, BgDark)
})
MainGrad.Rotation = 135

-- Top bar
local Top = Instance.new("Frame", Main)
Top.Size = UDim2.new(1, 0, 0, 50)
Top.BackgroundColor3 = BgDark
Top.BorderSizePixel = 0
Instance.new("UICorner", Top).CornerRadius = UDim.new(0, 14)

local TopGrad = Instance.new("UIGradient", Top)
TopGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(55, 35, 110)),
    ColorSequenceKeypoint.new(0.5, BgDark),
    ColorSequenceKeypoint.new(1, BgDark)
})
TopGrad.Rotation = 0

local AccentLine = Instance.new("Frame", Top)
AccentLine.Size = UDim2.new(1, 0, 0, 1)
AccentLine.Position = UDim2.new(0, 0, 1, -1)
AccentLine.BackgroundColor3 = Accent
AccentLine.BorderSizePixel = 0
local AccentLineGrad = Instance.new("UIGradient", AccentLine)
AccentLineGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0,0,0)),
    ColorSequenceKeypoint.new(0.2, Accent),
    ColorSequenceKeypoint.new(0.8, Accent2),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0,0,0))
})

-- Logo
local LogoBox = Instance.new("Frame", Top)
LogoBox.Size = UDim2.new(0, 36, 0, 36)
LogoBox.Position = UDim2.new(0, 10, 0.5, -18)
LogoBox.BackgroundTransparency = 1
LogoBox.BorderSizePixel = 0
local LogoImg = createLogo(LogoBox, 2)

-- Title
local TitleFrame = Instance.new("Frame", Top)
TitleFrame.Size = UDim2.new(1, -140, 1, 0)
TitleFrame.Position = UDim2.new(0, 54, 0, 0)
TitleFrame.BackgroundTransparency = 1

local Title = Instance.new("TextLabel", TitleFrame)
Title.Size = UDim2.new(1, 0, 0.55, 0)
Title.BackgroundTransparency = 1
Title.Text = "MOMO HUB  V30"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 17
Title.TextXAlignment = Enum.TextXAlignment.Left

local SubTitle = Instance.new("TextLabel", TitleFrame)
SubTitle.Size = UDim2.new(1, 0, 0.45, 0)
SubTitle.Position = UDim2.new(0, 0, 0.55, 0)
SubTitle.BackgroundTransparency = 1
SubTitle.Text = "V30  •  Arsenal Edition"
SubTitle.TextColor3 = Accent2
SubTitle.Font = Enum.Font.Gotham
SubTitle.TextSize = 11
SubTitle.TextXAlignment = Enum.TextXAlignment.Left

-- Hide button
local HideBtn = Instance.new("TextButton", Top)
HideBtn.Size = UDim2.new(0, 28, 0, 28)
HideBtn.Position = UDim2.new(1, -40, 0.5, -14)
HideBtn.BackgroundColor3 = Color3.fromRGB(50, 20, 20)
HideBtn.Text = "X"
HideBtn.TextColor3 = Color3.fromRGB(255, 80, 80)
HideBtn.Font = Enum.Font.GothamBold
HideBtn.TextSize = 13
Instance.new("UICorner", HideBtn).CornerRadius = UDim.new(1, 0)
local HideStroke = Instance.new("UIStroke", HideBtn)
HideStroke.Color = Color3.fromRGB(255, 80, 80)
HideStroke.Thickness = 1
HideStroke.Transparency = 0.6

-- IconBtn (minimize)
local IconBtn = Instance.new("TextButton", SG)
IconBtn.Size = UDim2.new(0, 54, 0, 54)
IconBtn.Position = UDim2.new(0, 16, 0, 16)
IconBtn.BackgroundColor3 = BgDark
IconBtn.Text = ""
IconBtn.Visible = false
IconBtn.BorderSizePixel = 0
Instance.new("UICorner", IconBtn).CornerRadius = UDim.new(0, 14)
local IconStroke = Instance.new("UIStroke", IconBtn)
IconStroke.Color = Accent
IconStroke.Thickness = 1.5
IconStroke.Transparency = 0.2
createLogo(IconBtn, 1)

local PulseRing = Instance.new("Frame", SG)
PulseRing.Size = UDim2.new(0, 54, 0, 54)
PulseRing.Position = UDim2.new(0, 16, 0, 16)
PulseRing.BackgroundTransparency = 1
PulseRing.BorderSizePixel = 0
Instance.new("UICorner", PulseRing).CornerRadius = UDim.new(0, 14)
local PulseStroke = Instance.new("UIStroke", PulseRing)
PulseStroke.Color = Accent2
PulseStroke.Thickness = 2
PulseStroke.Transparency = 1
PulseRing.Visible = false

local function doPulse()
    if not IconBtn.Visible then return end
    PulseRing.Visible = true
    PulseStroke.Transparency = 0.1
    PulseRing.Size = UDim2.new(0, 54, 0, 54)
    PulseRing.Position = UDim2.new(0, 16, 0, 16)
    T:Create(PulseRing, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, 80, 0, 80),
        Position = UDim2.new(0, 3, 0, 3)
    }):Play()
    T:Create(PulseStroke, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
        Transparency = 1
    }):Play()
end

task.spawn(function()
    while true do
        task.wait(1.5)
        doPulse()
    end
end)

HideBtn.MouseButton1Click:Connect(function()
    Main.Visible = false
    IconBtn.Visible = true
    PulseRing.Visible = true
end)
IconBtn.MouseButton1Click:Connect(function()
    Main.Visible = true
    IconBtn.Visible = false
    PulseRing.Visible = false
end)

LogoBox.MouseEnter:Connect(function()
    T:Create(LogoImg, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(31, 41, 55)}):Play()
end)
LogoBox.MouseLeave:Connect(function()
    T:Create(LogoImg, TweenInfo.new(0.2), {BackgroundColor3 = BgDark}):Play()
end)

-- Sidebar
local TabF = Instance.new("Frame", Main)
TabF.Size = UDim2.new(0, 115, 1, -50)
TabF.Position = UDim2.new(0, 0, 0, 50)
TabF.BackgroundColor3 = Color3.fromRGB(12, 17, 28)
TabF.BackgroundTransparency = 0
TabF.BorderSizePixel = 0
local SidebarLine = Instance.new("Frame", TabF)
SidebarLine.Size = UDim2.new(0, 1, 1, 0)
SidebarLine.Position = UDim2.new(1, -1, 0, 0)
SidebarLine.BackgroundColor3 = Accent
SidebarLine.BorderSizePixel = 0
local SidebarLineGrad = Instance.new("UIGradient", SidebarLine)
SidebarLineGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0,0,0)),
    ColorSequenceKeypoint.new(0.3, Accent),
    ColorSequenceKeypoint.new(0.7, Accent2),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0,0,0))
})
SidebarLineGrad.Rotation = 90

local Scroll = Instance.new("ScrollingFrame", Main)
Scroll.Size = UDim2.new(1, -124, 1, -60)
Scroll.Position = UDim2.new(0, 120, 0, 55)
Scroll.BackgroundTransparency = 1
Scroll.ScrollBarThickness = 2
Scroll.ScrollBarImageColor3 = Accent
Scroll.BorderSizePixel = 0
Scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
Scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
local ScrollPad = Instance.new("UIPadding", Scroll)
ScrollPad.PaddingTop = UDim.new(0, 4)
ScrollPad.PaddingRight = UDim.new(0, 4)

local Layout = Instance.new("UIListLayout", Scroll)
Layout.Padding = UDim.new(0, 7)

local tabs = {Combat = {}, Move = {}, Visuals = {}, Arsenal = {}, Settings = {}}

local function switchTab(name)
    for _, v in pairs(Scroll:GetChildren()) do
        if v:IsA("Frame") or v:IsA("TextButton") then v.Visible = false end
    end
    for _, v in pairs(tabs[name]) do v.Visible = true end
end

local tabIcons = {Combat = "⚔", Move = "🏃", Visuals = "👁", Arsenal = "🎯", Settings = "⚙"}
local tabNames = {"Combat", "Move", "Visuals", "Arsenal", "Settings"}
local tabBtns = {}

local function makeTab(n, y)
    local wrapper = Instance.new("Frame", TabF)
    wrapper.Size = UDim2.new(1, -8, 0, 38)
    wrapper.Position = UDim2.new(0, 4, 0, y)
    wrapper.BackgroundTransparency = 1
    wrapper.BorderSizePixel = 0
    Instance.new("UICorner", wrapper).CornerRadius = UDim.new(0, 8)

    local indicator = Instance.new("Frame", wrapper)
    indicator.Size = UDim2.new(0, 3, 0.7, 0)
    indicator.Position = UDim2.new(0, 0, 0.15, 0)
    indicator.BackgroundColor3 = Accent2
    indicator.BorderSizePixel = 0
    indicator.Visible = false
    Instance.new("UICorner", indicator).CornerRadius = UDim.new(1, 0)

    local b = Instance.new("TextButton", wrapper)
    b.Size = UDim2.new(1, 0, 1, 0)
    b.BackgroundColor3 = Accent
    b.BackgroundTransparency = 1
    b.Text = ""
    b.BorderSizePixel = 0
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)

    local iconLbl = Instance.new("TextLabel", b)
    iconLbl.Size = UDim2.new(0, 24, 1, 0)
    iconLbl.Position = UDim2.new(0, 10, 0, 0)
    iconLbl.BackgroundTransparency = 1
    iconLbl.Text = tabIcons[n] or "•"
    iconLbl.TextColor3 = Color3.fromRGB(120, 120, 120)
    iconLbl.Font = Enum.Font.GothamBold
    iconLbl.TextSize = 14

    local nameLbl = Instance.new("TextLabel", b)
    nameLbl.Size = UDim2.new(1, -38, 1, 0)
    nameLbl.Position = UDim2.new(0, 36, 0, 0)
    nameLbl.BackgroundTransparency = 1
    nameLbl.Text = n
    nameLbl.TextColor3 = Color3.fromRGB(130, 130, 130)
    nameLbl.Font = Enum.Font.GothamBold
    nameLbl.TextSize = 12
    nameLbl.TextXAlignment = Enum.TextXAlignment.Left

    b.MouseEnter:Connect(function()
        if b.BackgroundTransparency ~= 0.8 then
            T:Create(b, TweenInfo.new(0.15), {BackgroundTransparency = 0.85}):Play()
        end
    end)
    b.MouseLeave:Connect(function()
        if b.BackgroundTransparency ~= 0.8 then
            T:Create(b, TweenInfo.new(0.15), {BackgroundTransparency = 1}):Play()
        end
    end)

    b.MouseButton1Click:Connect(function()
        switchTab(n)
        for _, data in pairs(tabBtns) do
            T:Create(data.btn, TweenInfo.new(0.2), {BackgroundTransparency = 1}):Play()
            T:Create(data.icon, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(120,120,120)}):Play()
            T:Create(data.name, TweenInfo.new(0.2), {TextColor3 = Color3.fromRGB(130,130,130)}):Play()
            data.indicator.Visible = false
        end
        T:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.8}):Play()
        T:Create(iconLbl, TweenInfo.new(0.2), {TextColor3 = OnText}):Play()
        T:Create(nameLbl, TweenInfo.new(0.2), {TextColor3 = OnText}):Play()
        indicator.Visible = true
    end)

    tabBtns[n] = {btn = b, icon = iconLbl, name = nameLbl, indicator = indicator}
    return tabBtns[n]
end

for i, name in ipairs(tabNames) do
    makeTab(name, (i-1)*44 + 10)
end

-- Toggle Builder
local function makeTog(n, tab, def, cb)
    local f = Instance.new("TextButton", Scroll)
    f.Size = UDim2.new(1, 0, 0, 35)
    f.BackgroundColor3 = DarkGrey
    f.BackgroundTransparency = 0.3
    f.Text = ""
    f.Visible = false
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 6)
    local lbl = Instance.new("TextLabel", f)
    lbl.Size = UDim2.new(1, -60, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = n
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    local ind = Instance.new("Frame", f)
    ind.Size = UDim2.new(0, 36, 0, 18)
    ind.Position = UDim2.new(1, -48, 0.5, -9)
    ind.BackgroundColor3 = def and Accent or Color3.fromRGB(60,60,60)
    ind.BorderSizePixel = 0
    Instance.new("UICorner", ind).CornerRadius = UDim.new(1, 0)
    local dot = Instance.new("Frame", ind)
    dot.Size = UDim2.new(0, 14, 0, 14)
    dot.Position = def and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
    dot.BackgroundColor3 = def and OnText or Color3.fromRGB(150,150,150)
    dot.BorderSizePixel = 0
    Instance.new("UICorner", dot).CornerRadius = UDim.new(1, 0)
    local s = def
    f.MouseButton1Click:Connect(function()
        s = not s
        T:Create(ind, TweenInfo.new(0.2), {BackgroundColor3 = s and Accent or Color3.fromRGB(60,60,60)}):Play()
        T:Create(dot, TweenInfo.new(0.2), {Position = s and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7), BackgroundColor3 = s and OnText or Color3.fromRGB(150,150,150)}):Play()
        cb(s)
    end)
    table.insert(tabs[tab], f)
    return f
end

-- Slider Builder
local function makeSlider(n, tab, min, max, def, cb)
    local f = Instance.new("Frame", Scroll)
    f.Size = UDim2.new(1, 0, 0, 44)
    f.BackgroundColor3 = DarkGrey
    f.BackgroundTransparency = 0.3
    f.Visible = false
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 6)
    local lbl = Instance.new("TextLabel", f)
    lbl.Size = UDim2.new(1, -12, 0.5, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = n .. ": " .. def
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    local bar = Instance.new("Frame", f)
    bar.Size = UDim2.new(1, -24, 0, 4)
    bar.Position = UDim2.new(0, 12, 1, -10)
    bar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    Instance.new("UICorner", bar).CornerRadius = UDim.new(1, 0)
    local fill = Instance.new("Frame", bar)
    fill.Size = UDim2.new((def-min)/(max-min), 0, 1, 0)
    fill.BackgroundColor3 = Accent
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)
    local fillGrad = Instance.new("UIGradient", fill)
    fillGrad.Color = ColorSequence.new(Accent, Accent2)
    local dragging = false
    local function update(x)
        local percent = math.clamp((x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
        local val = math.floor(min + (max - min) * percent)
        fill.Size = UDim2.new(percent, 0, 1, 0)
        lbl.Text = n .. ": " .. val
        cb(val)
    end
    f.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true; update(input.Position.X) end
    end)
    U.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then update(input.Position.X) end
    end)
    U.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
    table.insert(tabs[tab], f)
    return f
end

-- Dropdown Builder
local function makeDropdown(n, tab, options, def, cb)
    local f = Instance.new("Frame", Scroll)
    f.Size = UDim2.new(1, 0, 0, 35)
    f.BackgroundColor3 = DarkGrey
    f.BackgroundTransparency = 0.3
    f.Visible = false
    f.ClipsDescendants = false
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 6)
    local lbl = Instance.new("TextLabel", f)
    lbl.Size = UDim2.new(0.5, 0, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = n
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    local btn = Instance.new("TextButton", f)
    btn.Size = UDim2.new(0, 110, 0, 25)
    btn.Position = UDim2.new(1, -120, 0.5, -12)
    btn.BackgroundColor3 = Color3.fromRGB(55, 65, 81)
    btn.Text = def
    btn.TextColor3 = Accent2
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
    local open = false
    local optFrames = {}
    for i, opt in ipairs(options) do
        local ob = Instance.new("TextButton", f)
        ob.Size = UDim2.new(0, 110, 0, 25)
        ob.Position = UDim2.new(1, -120, 0, 35 + (i-1)*27)
        ob.BackgroundColor3 = Color3.fromRGB(45, 55, 72)
        ob.Text = opt
        ob.TextColor3 = Color3.fromRGB(220,220,220)
        ob.Font = Enum.Font.Gotham
        ob.TextSize = 12
        ob.Visible = false
        ob.ZIndex = 10
        Instance.new("UICorner", ob).CornerRadius = UDim.new(0, 5)
        ob.MouseButton1Click:Connect(function()
            btn.Text = opt
            cb(opt)
            for _, v in pairs(optFrames) do v.Visible = false end
            open = false
        end)
        table.insert(optFrames, ob)
    end
    btn.MouseButton1Click:Connect(function()
        open = not open
        for _, v in pairs(optFrames) do v.Visible = open end
    end)
    table.insert(tabs[tab], f)
    return f
end

-- Color Cycle Button
local Colors = {
    Color3.fromRGB(255, 0, 0), Color3.fromRGB(0, 255, 0), Color3.fromRGB(0, 0, 255),
    Color3.fromRGB(255, 255, 0), Color3.fromRGB(255, 0, 255), Color3.fromRGB(0, 255, 255),
    Color3.fromRGB(255, 255, 255), Color3.fromRGB(139, 92, 246)
}
local function makeColor(n, tab, def, cb)
    local f = Instance.new("TextButton", Scroll)
    f.Size = UDim2.new(1, 0, 0, 35)
    f.BackgroundColor3 = DarkGrey
    f.BackgroundTransparency = 0.3
    f.Text = ""
    f.Visible = false
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 6)
    local lbl = Instance.new("TextLabel", f)
    lbl.Size = UDim2.new(1, -60, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = n
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    local ind = Instance.new("Frame", f)
    ind.Size = UDim2.new(0, 20, 0, 20)
    ind.Position = UDim2.new(1, -40, 0.5, -10)
    ind.BackgroundColor3 = def
    Instance.new("UICorner", ind).CornerRadius = UDim.new(1, 0)
    local idx = 1
    f.MouseButton1Click:Connect(function()
        idx = idx + 1
        if idx > #Colors then idx = 1 end
        ind.BackgroundColor3 = Colors[idx]
        cb(Colors[idx])
    end)
    table.insert(tabs[tab], f)
    return f
end

-- Keybind Builder
local function makeKeybind(n, tab, key, cb)
    local f = Instance.new("Frame", Scroll)
    f.Size = UDim2.new(1, 0, 0, 35)
    f.BackgroundColor3 = DarkGrey
    f.BackgroundTransparency = 0.3
    f.Visible = false
    Instance.new("UICorner", f).CornerRadius = UDim.new(0, 6)
    local lbl = Instance.new("TextLabel", f)
    lbl.Size = UDim2.new(0.6, 0, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = n
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    local btn = Instance.new("TextButton", f)
    btn.Size = UDim2.new(0, 80, 0, 24)
    btn.Position = UDim2.new(1, -90, 0.5, -12)
    btn.BackgroundColor3 = Color3.fromRGB(55, 65, 81)
    btn.Text = (tostring(key):gsub("Enum.KeyCode.", ""))
    btn.TextColor3 = Accent2
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 5)
    local waiting = false
    btn.MouseButton1Click:Connect(function()
        btn.Text = "..."
        btn.TextColor3 = Color3.fromRGB(255,255,100)
        waiting = true
    end)
    U.InputBegan:Connect(function(input, gp)
        if waiting and not gp and input.UserInputType == Enum.UserInputType.Keyboard then
            waiting = false
            btn.Text = (tostring(input.KeyCode):gsub("Enum.KeyCode.", ""))
            btn.TextColor3 = Accent2
            cb(input.KeyCode)
        end
    end)
    table.insert(tabs[tab], f)
end

-- ==================== NEON WEAPONS ====================
local neonParts = {}
local neonConns = {}
local neonHighlights = {}
local lastNeonScan = 0

local function applyNeonPart(p)
    if not p or not p:IsA("BasePart") then return end
    if neonParts[p] then return end
    neonParts[p] = math.random()
    p.Material = Enum.Material.Neon
    
    if C.NeonGlow then
        local hl = Instance.new("Highlight")
        hl.FillTransparency = 0.6
        hl.OutlineTransparency = 0.2
        hl.OutlineColor = Color3.new(1,1,1)
        hl.FillColor = Color3.new(1,1,1)
        hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        hl.Adornee = p
        hl.Parent = SG
        neonHighlights[p] = hl
    end
end

local function applyNeonTree(root)
    if not root then return end
    if root:IsA("BasePart") then applyNeonPart(root) end
    for _, d in ipairs(root:GetDescendants()) do
        if d:IsA("BasePart") then applyNeonPart(d) end
    end
    table.insert(neonConns, root.DescendantAdded:Connect(function(d)
        if d:IsA("BasePart") and C.NeonWeapons then applyNeonPart(d) end
    end))
end

local function clearNeon()
    for p, _ in pairs(neonParts) do
        if p and p.Parent then
            p.Material = Enum.Material.SmoothPlastic
        end
    end
    for p, hl in pairs(neonHighlights) do
        if hl then hl:Destroy() end
    end
    for _, c in ipairs(neonConns) do
        pcall(function() c:Disconnect() end)
    end
    neonParts = {}
    neonHighlights = {}
    neonConns = {}
end
-- =======================================================

-- COMBAT TAB
makeTog("Aimbot", "Combat", C.Aim, function(s) C.Aim = s end)
makeTog("Silent Aim", "Combat", C.SAim, function(s) C.SAim = s end)
makeTog("Kill Aura", "Combat", C.Kaura, function(s) C.Kaura = s end)
makeTog("Rapid Fire", "Combat", C.Rapid, function(s) C.Rapid = s end)
makeSlider("Smooth (1=instant,20=slow)", "Combat", 1, 20, 1, function(v) C.AimSmooth = v/100 end)
makeDropdown("Aim Bone", "Combat", {"Head", "Torso", "HumanoidRootPart"}, "Head", function(v) C.AimBone = v end)
makeSlider("Prediction", "Combat", 0, 20, 0, function(v) C.AimPrediction = v end)
makeTog("Trigger Bot", "Combat", C.TriggerBot, function(s) C.TriggerBot = s end)
makeTog("Hitbox Expander", "Combat", C.HitboxExpand, function(s) C.HitboxExpand = s end)
makeSlider("Hitbox Size", "Combat", 1, 20, C.HitboxSize, function(v) C.HitboxSize = v end)

-- MOVE TAB
local FlyConn, FlyBodyPos, FlyBodyGyro
makeTog("Fly", "Move", C.Fly, function(s)
    C.Fly = s
    if FlyConn then FlyConn:Disconnect() FlyConn = nil end
    if FlyBodyPos then FlyBodyPos:Destroy() FlyBodyPos = nil end
    if FlyBodyGyro then FlyBodyGyro:Destroy() FlyBodyGyro = nil end
    if s then
        local char = LP.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local hrp = char.HumanoidRootPart
        pcall(function() hrp:SetNetworkOwner(LP) end)
        FlyBodyPos = Instance.new("BodyPosition", hrp)
        FlyBodyPos.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        FlyBodyPos.P = 5000
        FlyBodyPos.D = 500
        FlyBodyPos.Position = hrp.Position
        FlyBodyGyro = Instance.new("BodyGyro", hrp)
        FlyBodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        FlyBodyGyro.P = 10000
        FlyBodyGyro.CFrame = hrp.CFrame
        char.Humanoid.PlatformStand = true
        FlyConn = R.RenderStepped:Connect(function()
            if not C.Fly or not char or not char.Parent then
                if FlyConn then FlyConn:Disconnect() end
                if FlyBodyPos then FlyBodyPos:Destroy() end
                if FlyBodyGyro then FlyBodyGyro:Destroy() end
                if char and char:FindFirstChild("Humanoid") then char.Humanoid.PlatformStand = false end
                return
            end
            local moveDir = Vector3.zero
            if U:IsKeyDown(Enum.KeyCode.W) then moveDir += W.CurrentCamera.CFrame.LookVector end
            if U:IsKeyDown(Enum.KeyCode.S) then moveDir -= W.CurrentCamera.CFrame.LookVector end
            if U:IsKeyDown(Enum.KeyCode.A) then moveDir -= W.CurrentCamera.CFrame.RightVector end
            if U:IsKeyDown(Enum.KeyCode.D) then moveDir += W.CurrentCamera.CFrame.RightVector end
            if U:IsKeyDown(Enum.KeyCode.Space) then moveDir += Vector3.new(0, 1, 0) end
            if U:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir -= Vector3.new(0, 1, 0) end
            if moveDir.Magnitude > 0 then
                FlyBodyPos.Position = hrp.Position + (moveDir.Unit * C.FlyVal)
            else
                FlyBodyPos.Position = hrp.Position
            end
            FlyBodyGyro.CFrame = W.CurrentCamera.CFrame
        end)
    else
        if LP.Character and LP.Character:FindFirstChild("Humanoid") then
            LP.Character.Humanoid.PlatformStand = false
        end
    end
end)
makeTog("Speed Hack", "Move", C.Speed, function(s) C.Speed = s end)
makeSlider("Speed Value", "Move", 16, 200, C.SpeedVal, function(v) C.SpeedVal = v end)
makeSlider("Fly Value", "Move", 50, 300, C.FlyVal, function(v) C.FlyVal = v end)
makeTog("Bhop", "Move", C.Bhop, function(s) C.Bhop = s end)
makeTog("Noclip", "Move", C.Noclip, function(s) C.Noclip = s end)
makeTog("GodMode", "Move", C.God, function(s) C.God = s end)
makeTog("Infinite Jump", "Move", C.InfJump, function(s) C.InfJump = s end)

-- VISUALS TAB
makeTog("Wallhack (Highlight)", "Visuals", true, function(s) espF.Parent = s and SG or nil end)
makeColor("ESP Color", "Visuals", C.ESPColor, function(c) C.ESPColor = c end)
makeTog("Tracers", "Visuals", true, function(s) C.Tracers = s end)
makeColor("Tracers Color", "Visuals", C.TracersColor, function(c) C.TracersColor = c end)
makeTog("Skeleton ESP", "Visuals", true, function(s) C.Skeleton = s end)
makeTog("Fullbright", "Visuals", C.Fullbright, function(s)
    L.Ambient = s and Color3.fromRGB(178,178,178) or Color3.fromRGB(0,0,0)
    L.OutdoorAmbient = s and Color3.fromRGB(178,178,178) or Color3.fromRGB(0,0,0)
end)
makeTog("POV Circle", "Visuals", C.POV, function(s) C.POV = s; POV.Visible = s end)
makeSlider("POV Size", "Visuals", 50, 500, C.POVSize, function(v) C.POVSize = v end)
makeColor("POV Color", "Visuals", C.POVColor, function(c) C.POVColor = c end)
makeTog("Rainbow Mode", "Visuals", C.Rainbow, function(s) C.Rainbow = s end)

-- NEON WEAPONS UI
makeTog("Neon Rainbow Weapons", "Visuals", C.NeonWeapons, function(s)
    C.NeonWeapons = s
    if not s then clearNeon() end
end)
makeTog("Thick Glow (Highlight)", "Visuals", C.NeonGlow, function(s)
    C.NeonGlow = s
    if not s then
        for p, hl in pairs(neonHighlights) do
            if hl then hl:Destroy() end
            neonHighlights[p] = nil
        end
    else
        for p, _ in pairs(neonParts) do
            if p and p.Parent and not neonHighlights[p] then
                local hl = Instance.new("Highlight")
                hl.FillTransparency = 0.6
                hl.OutlineTransparency = 0.2
                hl.OutlineColor = Color3.new(1,1,1)
                hl.FillColor = Color3.new(1,1,1)
                hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                hl.Adornee = p
                hl.Parent = SG
                neonHighlights[p] = hl
            end
        end
    end
end)
makeSlider("Neon Hue Speed (1-20)", "Visuals", 1, 20, 6, function(v) C.NeonHueSpeed = v / 10 end)

makeTog("Custom Crosshair", "Visuals", C.Crosshair, function(s)
    C.Crosshair = s
    CrossDot.Visible = s and C.CrosshairType == "Dot"
    for _, l in pairs(CrossLines) do l.Visible = s and C.CrosshairType == "Cross" end
end)
makeDropdown("Crosshair Type", "Visuals", {"Dot", "Cross"}, "Dot", function(v)
    C.CrosshairType = v
    CrossDot.Visible = C.Crosshair and v == "Dot"
    for _, l in pairs(CrossLines) do l.Visible = C.Crosshair and v == "Cross" end
end)
makeColor("Crosshair Color", "Visuals", C.CrosshairColor, function(c)
    C.CrosshairColor = c
    CrossDot.Color = c
    for _, l in pairs(CrossLines) do l.Color = c end
end)
makeSlider("Crosshair Size", "Visuals", 2, 20, C.CrosshairSize, function(v) C.CrosshairSize = v end)

makeTog("Aimbot FOV Circle", "Visuals", C.FOVCircle2, function(s) C.FOVCircle2 = s; POV2.Visible = s end)
makeSlider("Aimbot FOV Size", "Visuals", 50, 500, C.FOVCircle2Size, function(v) C.FOVCircle2Size = v end)
makeColor("Aimbot FOV Color", "Visuals", C.FOVCircle2Color, function(c) C.FOVCircle2Color = c end)

makeTog("Time Changer", "Visuals", C.TimeChanger, function(s) C.TimeChanger = s end)
makeSlider("Time of Day (0-24)", "Visuals", 0, 24, C.TimeVal, function(v) C.TimeVal = v; if C.TimeChanger then L.ClockTime = v end end)
makeTog("Fog Remover", "Visuals", C.FogRemover, function(s)
    C.FogRemover = s
    L.FogEnd = s and 1e6 or 100000
    L.FogStart = 0
end)
makeTog("Ambient Customizer", "Visuals", C.AmbientCustom, function(s) C.AmbientCustom = s end)
makeColor("Ambient Color", "Visuals", C.AmbientColor, function(c)
    C.AmbientColor = c
    if C.AmbientCustom then L.Ambient = c; L.OutdoorAmbient = c end
end)

makeTog("Health ESP", "Visuals", C.HealthESP, function(s) C.HealthESP = s end)
makeTog("Name ESP + Jarak", "Visuals", C.NameESP, function(s) C.NameESP = s end)
makeTog("Box ESP 2D", "Visuals", C.BoxESP, function(s) C.BoxESP = s end)
makeTog("Chams", "Visuals", C.Chams, function(s) C.Chams = s end)
makeColor("Chams Color", "Visuals", C.ChamsColor, function(c) C.ChamsColor = c end)
makeTog("Radar", "Visuals", C.Radar, function(s)
    C.Radar = s
    RadarFrame.Visible = s
end)

-- ARSENAL TAB
makeTog("Knife Bot", "Arsenal", C.KnifeBot, function(s) C.KnifeBot = s end)
makeSlider("Knife Range", "Arsenal", 5, 30, C.KnifeBotRange, function(v) C.KnifeBotRange = v end)
makeTog("Coin ESP", "Arsenal", C.CoinESP, function(s) C.CoinESP = s end)
makeTog("Auto Respawn", "Arsenal", C.AutoRespawn, function(s) C.AutoRespawn = s end)
makeTog("Anti AFK", "Arsenal", C.AntiAFK, function(s) C.AntiAFK = s end)

-- SETTINGS TAB
makeTog("Watermark", "Settings", C.WatermarkEnabled, function(s) C.WatermarkEnabled = s; WMLabel.Visible = s end)
makeTog("Hit Sound", "Settings", C.HitSound, function(s) C.HitSound = s end)
makeTog("Mute Footsteps", "Settings", C.MuteFootsteps, function(s)
    C.MuteFootsteps = s
    if LP.Character then
        for _, v in pairs(LP.Character:GetDescendants()) do
            if v:IsA("Sound") and (v.Name == "FootstepSound" or v.Name == "Running" or v.Name == "Splash" or v.Name:lower():find("step")) then
                v.Volume = s and 0 or 0.5
            end
        end
    end
end)
makeSlider("UI Transparency", "Settings", 0, 9, 3, function(v)
    C.UITransparency = v/10
    Main.BackgroundTransparency = v/10
end)

local kbNames = {"Fly", "Speed", "Noclip", "Aimbot"}
local kbDefaults = {
    Fly = Enum.KeyCode.F,
    Speed = Enum.KeyCode.G,
    Noclip = Enum.KeyCode.N,
    Aimbot = Enum.KeyCode.H,
}
for _, name in ipairs(kbNames) do
    makeKeybind("Keybind: " .. name, "Settings", kbDefaults[name], function(key)
        C.KB[name] = key
    end)
end

U.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == C.KB.Fly then
        C.Fly = not C.Fly
    elseif input.KeyCode == C.KB.Speed then
        C.Speed = not C.Speed
    elseif input.KeyCode == C.KB.Noclip then
        C.Noclip = not C.Noclip
    elseif input.KeyCode == C.KB.Aimbot then
        C.Aim = not C.Aim
    end
end)

-- Default tab
switchTab("Combat")
local defaultTab = tabBtns["Combat"]
defaultTab.btn.BackgroundTransparency = 0.8
defaultTab.icon.TextColor3 = OnText
defaultTab.name.TextColor3 = OnText
defaultTab.indicator.Visible = true

-- Drag window
local dragging, dragStart, startPos
Top.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false end
        end)
    end
end)
U.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- RENDER LOOP
local lastDmgHealth = {}
local lastFrameTick = tick()

R.RenderStepped:Connect(function()
    local nowTick = tick()
    local fps = math.floor(1 / math.max(nowTick - lastFrameTick, 0.001))
    lastFrameTick = nowTick

    local finalESP = C.ESPColor
    local finalTracers = C.TracersColor
    local finalPOV = C.POVColor
    if C.Rainbow then
        local rc = Color3.fromHSV(tick() % 1, 1, 1)
        finalESP = rc; finalTracers = rc; finalPOV = rc
    end

    local center = Vector2.new(W.CurrentCamera.ViewportSize.X/2, W.CurrentCamera.ViewportSize.Y/2)

    if C.POV then
        POV.Position = center
        POV.Radius = C.POVSize
        POV.Color = finalPOV
        POV.Visible = true
    else
        POV.Visible = false
    end

    if C.FOVCircle2 then
        POV2.Position = center
        POV2.Radius = C.FOVCircle2Size
        POV2.Color = C.FOVCircle2Color
        POV2.Visible = true
    else
        POV2.Visible = false
    end

    if C.Crosshair then
        if C.CrosshairType == "Dot" then
            CrossDot.Position = center
            CrossDot.Radius = C.CrosshairSize
            CrossDot.Color = C.CrosshairColor
            CrossDot.Visible = true
            for _, l in pairs(CrossLines) do l.Visible = false end
        else
            CrossDot.Visible = false
            local sz = C.CrosshairSize * 3
            local gap = C.CrosshairSize
            CrossLines[1].From = Vector2.new(center.X, center.Y - gap)
            CrossLines[1].To = Vector2.new(center.X, center.Y - sz)
            CrossLines[2].From = Vector2.new(center.X, center.Y + gap)
            CrossLines[2].To = Vector2.new(center.X, center.Y + sz)
            CrossLines[3].From = Vector2.new(center.X - gap, center.Y)
            CrossLines[3].To = Vector2.new(center.X - sz, center.Y)
            CrossLines[4].From = Vector2.new(center.X + gap, center.Y)
            CrossLines[4].To = Vector2.new(center.X + sz, center.Y)
            for _, l in pairs(CrossLines) do
                l.Color = C.CrosshairColor
                l.Visible = true
            end
        end
    else
        CrossDot.Visible = false
        for _, l in pairs(CrossLines) do l.Visible = false end
    end

    if C.TimeChanger then L.ClockTime = C.TimeVal end
    if C.AmbientCustom then
        L.Ambient = C.AmbientColor
        L.OutdoorAmbient = C.AmbientColor
    end

    if C.WatermarkEnabled then
        local ok, ping = pcall(function() return math.floor(LP:GetNetworkPing()*1000) end)
        if not ok then ping = 0 end
        WMLabel.Text = string.format(" MOMO V30  |  %s  |  %d FPS  |  %dms", LP.Name, fps, ping)
    end

    -- Aimbot
    if C.Aim then
        local t = getTarget()
        if t then
            local bone = getBonePart(t.Character, C.AimBone)
            if bone then
                local targetPos = bone.Position
                if C.AimPrediction > 0 and t.Character:FindFirstChild("HumanoidRootPart") then
                    local vel = t.Character.HumanoidRootPart.Velocity
                    targetPos = targetPos + vel * (C.AimPrediction * 0.016)
                end
                local targetCF = CFrame.new(W.CurrentCamera.CFrame.Position, targetPos)
                W.CurrentCamera.CFrame = W.CurrentCamera.CFrame:Lerp(targetCF, math.clamp(1 - C.AimSmooth, 0.01, 1))
            end
        end
    end

    if LP.Character then
        local char = LP.Character
        local hum = char:FindFirstChild("Humanoid")
        local hrpSelf = char:FindFirstChild("HumanoidRootPart")

        if C.God and hum then
            for _, p in pairs(char:GetDescendants()) do
                if p:IsA("BasePart") then p.CanQuery = false; p.CanCollide = false end
            end
            if hum:GetState() == Enum.HumanoidStateType.Dead then hum:ChangeState(Enum.HumanoidStateType.Running) end
        end

        if C.Noclip then
            for _, p in pairs(char:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
        end

        if C.Speed and hum then hum.WalkSpeed = C.SpeedVal end
        if not C.Speed and hum and hum.WalkSpeed == C.SpeedVal then hum.WalkSpeed = 16 end

        if C.InfJump and hum and U:IsKeyDown(Enum.KeyCode.Space) then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end

        if C.Bhop and hum and hum:GetState() == Enum.HumanoidStateType.Landed and U:IsKeyDown(Enum.KeyCode.Space) then
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
        end

        local tool = char:FindFirstChildWhichIsA("Tool")
        if tool then
            if C.Rapid and U:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then tool:Activate() end
            if C.AutoReload then
                for _, v in pairs(tool:GetDescendants()) do
                    if v:IsA("NumberValue") and (v.Name == "Ammo" or v.Name == "CurrentAmmo") and v.Value < 1 then tool:Activate() end
                end
            end
            if C.TriggerBot then
                local t = getTarget()
                if t then
                    local bone = getBonePart(t.Character, C.AimBone)
                    if bone then
                        local sp, onScreen = W.CurrentCamera:WorldToViewportPoint(bone.Position)
                        if onScreen then
                            local dist = (Vector2.new(sp.X, sp.Y) - center).Magnitude
                            if dist < 15 then tool:Activate() end
                        end
                    end
                end
            end
        end

        if C.Kaura then
            for _, v in pairs(P:GetPlayers()) do
                if v ~= LP and isEnemy(v) and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                    if hrpSelf and (hrpSelf.Position - v.Character.HumanoidRootPart.Position).Magnitude < 15 and tool then tool:Activate() end
                end
            end
        end

        if C.KnifeBot and hrpSelf then
            for _, v in pairs(P:GetPlayers()) do
                if v ~= LP and isEnemy(v) and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = (hrpSelf.Position - v.Character.HumanoidRootPart.Position).Magnitude
                    if dist < C.KnifeBotRange then
                        local knife = char:FindFirstChild("Knife") or char:FindFirstChild("knife")
                        if not knife then
                            for _, t2 in pairs(char:GetChildren()) do
                                if t2:IsA("Tool") and t2.Name:lower():find("knife") then knife = t2 break end
                            end
                        end
                        if knife then knife:Activate() end
                    end
                end
            end
        end

        if C.AutoRespawn and hum and hum.Health <= 0 then
            task.delay(0.5, function()
                local rs = game:GetService("ReplicatedStorage")
                local respawnEvent = rs:FindFirstChild("Respawn") or rs:FindFirstChildWhichIsA("RemoteEvent")
                if respawnEvent then pcall(function() respawnEvent:FireServer() end) end
            end)
        end
    end

    if C.AntiAFK then
        local vChar = LP.Character
        if vChar and vChar:FindFirstChild("HumanoidRootPart") then
            vChar.HumanoidRootPart.CFrame = vChar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 0.001)
        end
    end

    -- Hitbox Expander
    if C.HitboxExpand then
        for _, v in pairs(P:GetPlayers()) do
            if v ~= LP and isEnemy(v) and v.Character then
                local head = v.Character:FindFirstChild("Head")
                if head then head.Size = Vector3.new(C.HitboxSize, C.HitboxSize, C.HitboxSize) end
            end
        end
    else
        for _, v in pairs(P:GetPlayers()) do
            if v ~= LP and v.Character then
                local head = v.Character:FindFirstChild("Head")
                if head and head.Size.X ~= 1 then head.Size = Vector3.new(1, 1, 1) end
            end
        end
    end

    -- Chams
    if C.Chams then
        for _, v in pairs(P:GetPlayers()) do
            if v ~= LP and isEnemy(v) and v.Character then
                for _, p in pairs(v.Character:GetDescendants()) do
                    if p:IsA("BasePart") then
                        p.Color = C.ChamsColor
                        p.Material = Enum.Material.Neon
                    end
                end
            end
        end
    end

    -- ==================== NEON WEAPONS LOGIC ====================
    if C.NeonWeapons then
        local neonNow = tick()
        if neonNow - lastNeonScan > 0.3 then
            lastNeonScan = neonNow
            local cam = W.CurrentCamera
            if cam then
                for _, c in ipairs(cam:GetChildren()) do
                    if c:IsA("Model") or c.Name:lower():find("gun") or c.Name:lower():find("weapon") or c.Name:lower():find("view") then
                        applyNeonTree(c)
                    end
                end
            end
            if LP.Character then
                for _, c in ipairs(LP.Character:GetChildren()) do
                    if c:IsA("Tool") then applyNeonTree(c) end
                end
            end
            local pf = W:FindFirstChild(LP.Name)
            if pf then
                for _, c in ipairs(pf:GetChildren()) do
                    if c:IsA("Model") then applyNeonTree(c) end
                end
            end
            for _, c in ipairs(W:GetChildren()) do
                if c:IsA("Model") and c.Name:lower():find("view") then
                    applyNeonTree(c)
                end
            end
        end

        for p, phase in pairs(neonParts) do
            if p and p.Parent then
                local hue = (tick() * C.NeonHueSpeed + phase) % 1
                local color = Color3.fromHSV(hue, 1, 1)
                pcall(function() p.Color = color end)
                
                if neonHighlights[p] then
                    neonHighlights[p].FillColor = color
                    neonHighlights[p].OutlineColor = color
                end
            else
                neonParts[p] = nil
                if neonHighlights[p] then
                    neonHighlights[p]:Destroy()
                    neonHighlights[p] = nil
                end
            end
        end
    end
    -- ============================================================

    -- Coin ESP
    if C.CoinESP then
        for _, obj in pairs(W:GetDescendants()) do
            if obj:IsA("BasePart") then
                local nm = obj.Name:lower()
                if nm:find("coin") or nm:find("medal") then
                    if not obj:FindFirstChild("_CoinHL") then
                        local hl = Instance.new("SelectionBox", obj)
                        hl.Name = "_CoinHL"
                        hl.Adornee = obj
                        hl.Color3 = Color3.fromRGB(255, 215, 0)
                        hl.LineThickness = 0.03
                        hl.SurfaceTransparency = 0.7
                        hl.SurfaceColor3 = Color3.fromRGB(255, 215, 0)
                    end
                end
            end
        end
    end

    -- Radar
    if C.Radar and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
        local selfHRP = LP.Character.HumanoidRootPart
        local radarRange = 150
        local dotIdx = 1
        for _, v in pairs(P:GetPlayers()) do
            if v ~= LP and isEnemy(v) and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                local eHRP = v.Character.HumanoidRootPart
                local diff = eHRP.Position - selfHRP.Position
                local camCF = W.CurrentCamera.CFrame
                local relX = diff:Dot(camCF.RightVector)
                local relZ = diff:Dot(camCF.LookVector)
                local nx = math.clamp(relX / radarRange, -1, 1)
                local nz = math.clamp(-relZ / radarRange, -1, 1)
                local dot = radarDotPool[dotIdx]
                if dot then
                    dot.Position = UDim2.new(0.5 + nx*0.45, -3, 0.5 + nz*0.45, -3)
                    dot.Visible = true
                    dotIdx = dotIdx + 1
                end
            end
        end
        for i = dotIdx, #radarDotPool do
            radarDotPool[i].Visible = false
        end
    else
        for _, dot in pairs(radarDotPool) do dot.Visible = false end
    end

    -- ESP loop
    local screenSize = W.CurrentCamera.ViewportSize
    local bottomCenter = Vector2.new(screenSize.X / 2, screenSize.Y)

    for _, v in pairs(P:GetPlayers()) do
        local data = D[v.Name]
        if data and v.Character and v.Character:FindFirstChild("Head") and LP.Character then
            local enemyHum = v.Character:FindFirstChild("Humanoid")
            local head = v.Character:FindFirstChild("Head")
            local feet = v.Character:FindFirstChild("HumanoidRootPart")
            local enemy = isEnemy(v)

            if C.HitSound and enemyHum and enemy then
                if not lastDmgHealth[v.Name] then lastDmgHealth[v.Name] = enemyHum.Health end
                if enemyHum.Health < lastDmgHealth[v.Name] then HitSFX:Play() end
                lastDmgHealth[v.Name] = enemyHum.Health
            end

            -- Tracers
            if C.Tracers and enemy then
                local sp, os = W.CurrentCamera:WorldToViewportPoint(head.Position)
                if os then
                    data.line.From = bottomCenter
                    data.line.To = Vector2.new(sp.X, sp.Y)
                    data.line.Color = finalTracers
                    data.line.Visible = true
                else data.line.Visible = false end
            else data.line.Visible = false end

            -- Skeleton
            if C.Skeleton and enemy and enemyHum then
                local function getPos(n)
                    local p = v.Character:FindFirstChild(n)
                    if p then
                        local sp, os = W.CurrentCamera:WorldToViewportPoint(p.Position)
                        if os then return Vector2.new(sp.X, sp.Y) end
                    end
                    return nil
                end
                local hd = getPos("Head")
                local hrp = getPos("HumanoidRootPart")
                local lH = getPos("LeftHand") or getPos("Left Arm")
                local rH = getPos("RightHand") or getPos("Right Arm")
                local lF = getPos("LeftFoot") or getPos("Left Leg")
                local rF = getPos("RightFoot") or getPos("Right Leg")
                if hd and hrp and lH and rH and lF and rF then
                    local parts = {{hrp,hd},{hrp,lH},{hrp,rH},{hrp,lF},{hrp,rF}}
                    for i, line in pairs(data.skeleton) do
                        if parts[i] then
                            line.From = parts[i][1]; line.To = parts[i][2]
                            line.Color = finalESP; line.Visible = true
                        else line.Visible = false end
                    end
                else
                    for _, line in pairs(data.skeleton) do line.Visible = false end
                end
            else
                for _, line in pairs(data.skeleton) do line.Visible = false end
            end

            -- Health, Name, Box
            if enemy and head and feet and data.hbBg then
                local headSP, headOS = W.CurrentCamera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.6, 0))
                local feetSP, feetOS = W.CurrentCamera:WorldToViewportPoint(feet.Position - Vector3.new(0, 2.5, 0))

                if headOS and feetOS then
                    local topY = headSP.Y
                    local botY = feetSP.Y
                    local midX = headSP.X
                    local height = botY - topY
                    local width = height * 0.5

                    if C.BoxESP and data.box then
                        local l, r, t2, b2 = midX - width/2, midX + width/2, topY, botY
                        data.box[1].From = Vector2.new(l, t2); data.box[1].To = Vector2.new(r, t2)
                        data.box[2].From = Vector2.new(l, b2); data.box[2].To = Vector2.new(r, b2)
                        data.box[3].From = Vector2.new(l, t2); data.box[3].To = Vector2.new(l, b2)
                        data.box[4].From = Vector2.new(r, t2); data.box[4].To = Vector2.new(r, b2)
                        for _, bl in pairs(data.box) do bl.Color = finalESP; bl.Visible = true end
                    else
                        if data.box then for _, bl in pairs(data.box) do bl.Visible = false end end
                    end

                    if C.HealthESP and enemyHum and data.hbBg then
                        local hpRatio = math.clamp(enemyHum.Health / enemyHum.MaxHealth, 0, 1)
                        local barX = midX - width/2 - 6
                        data.hbBg.From = Vector2.new(barX, topY)
                        data.hbBg.To = Vector2.new(barX, botY)
                        data.hbBg.Visible = true
                        data.hbFill.From = Vector2.new(barX, botY)
                        data.hbFill.To = Vector2.new(barX, botY - height * hpRatio)
                        data.hbFill.Color = Color3.fromHSV(hpRatio * 0.33, 1, 1)
                        data.hbFill.Visible = true
                    else
                        if data.hbBg then data.hbBg.Visible = false end
                        if data.hbFill then data.hbFill.Visible = false end
                    end

                    if C.NameESP and data.nameDraw and LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
                        local dist = math.floor((LP.Character.HumanoidRootPart.Position - feet.Position).Magnitude)
                        data.nameDraw.Text = v.Name .. " [" .. dist .. "m]"
                        data.nameDraw.Position = Vector2.new(midX - 40, topY - 16)
                        data.nameDraw.Visible = true
                    else
                        if data.nameDraw then data.nameDraw.Visible = false end
                    end
                else
                    if data.hbBg then data.hbBg.Visible = false end
                    if data.hbFill then data.hbFill.Visible = false end
                    if data.nameDraw then data.nameDraw.Visible = false end
                    if data.box then for _, bl in pairs(data.box) do bl.Visible = false end end
                end
            else
                if data.hbBg then data.hbBg.Visible = false end
                if data.hbFill then data.hbFill.Visible = false end
                if data.nameDraw then data.nameDraw.Visible = false end
                if data.box then for _, bl in pairs(data.box) do bl.Visible = false end end
            end
        end
    end
end)

-- ==================== CHEATER DETECTOR ====================
C.CheaterDetect = true
C.CheaterTracer = true
C.CheaterSpeedLimit = 45
C.CheaterFlagTime = 10

local CD = {}
local CD_COLOR = Color3.fromRGB(255, 140, 0)

local function getCD(v)
    if not CD[v] then
        local line = Drawing.new("Line")
        line.Thickness = 2.5
        line.Color = CD_COLOR
        line.Visible = false
        local txt = Drawing.new("Text")
        txt.Size = 14
        txt.Center = true
        txt.Outline = true
        txt.Color = CD_COLOR
        txt.Visible = false
        CD[v] = {
            line = line, txt = txt, char = nil,
            lastPos = nil, lastT = os.clock(), spawnT = 0,
            airTime = 0, speedTime = 0, reasons = {}
        }
    end
    return CD[v]
end

local function resetCD(d)
    d.lastPos = nil
    d.airTime = 0
    d.speedTime = 0
end

local function flagCheater(v, d, reason)
    local now = os.clock()
    local old = d.reasons[reason]
    if not old or old < now then
        pcall(function()
            game:GetService("StarterGui"):SetCore("SendNotification", {
                Title = "Cheater Detector",
                Text = v.Name .. " mencurigakan: " .. reason,
                Duration = 4
            })
        end)
    end
    d.reasons[reason] = now + C.CheaterFlagTime
end

P.PlayerRemoving:Connect(function(v)
    local d = CD[v]
    if d then
        d.line:Remove()
        d.txt:Remove()
        CD[v] = nil
    end
end)

R.RenderStepped:Connect(function()
    local now = os.clock()
    local cam = W.CurrentCamera
    local vp = cam.ViewportSize
    local bottom = Vector2.new(vp.X / 2, vp.Y)

    for _, v in ipairs(P:GetPlayers()) do
        if v ~= LP then
            local d = getCD(v)
            local char = v.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            local hum = char and char:FindFirstChildOfClass("Humanoid")

            if C.CheaterDetect and hrp and hum and hum.Health > 0 then
                if d.char ~= char then
                    d.char = char
                    d.spawnT = now
                    resetCD(d)
                end

                local dt = now - d.lastT
                d.lastT = now
                local pos = hrp.Position
                local graceOver = (now - d.spawnT) > 2

                if d.lastPos and dt > 0 and dt < 0.5 and graceOver and not hum.SeatPart then
                    local delta = pos - d.lastPos

                    -- Teleport
                    if delta.Magnitude > 50 and dt < 0.2 then
                        flagCheater(v, d, "Teleport")
                    end

                    -- Speed (horizontal)
                    local hSpeed = Vector3.new(delta.X, 0, delta.Z).Magnitude / dt
                    if hSpeed > C.CheaterSpeedLimit and delta.Magnitude < 50 then
                        d.speedTime = d.speedTime + dt
                        if d.speedTime > 0.5 then flagCheater(v, d, "Speed") end
                    else
                        d.speedTime = math.max(0, d.speedTime - dt)
                    end

                    -- Fly
                    local vy = delta.Y / dt
                    if hum.FloorMaterial == Enum.Material.Air and vy > -5 then
                        d.airTime = d.airTime + dt
                        if d.airTime > 2.5 then flagCheater(v, d, "Fly") end
                    else
                        d.airTime = 0
                    end
                end
                d.lastPos = pos

                -- Hitbox (skip kalau hitbox expander lu sendiri nyala)
                if not C.HitboxExpand then
                    local head = char:FindFirstChild("Head")
                    if head then
                        local s = head.Size
                        if math.max(s.X, s.Y, s.Z) > 2.6 then
                            flagCheater(v, d, "Hitbox")
                        end
                    end
                end
            else
                d.lastT = now
                if not hrp then resetCD(d) end
            end

            -- Kumpulin alasan yang masih aktif
            local active = {}
            for reason, expiry in pairs(d.reasons) do
                if expiry > now then
                    table.insert(active, reason)
                else
                    d.reasons[reason] = nil
                end
            end

            local drawn = false
            if C.CheaterDetect and C.CheaterTracer and #active > 0 and hrp then
                local sp, onScreen = cam:WorldToViewportPoint(hrp.Position)
                if onScreen then
                    d.line.From = bottom
                    d.line.To = Vector2.new(sp.X, sp.Y)
                    d.line.Visible = true
                    d.txt.Text = "[SUS] " .. v.Name .. " (" .. table.concat(active, ", ") .. ")"
                    d.txt.Position = Vector2.new(sp.X, sp.Y - 40)
                    d.txt.Visible = true
                    drawn = true
                end
            end
            if not drawn then
                d.line.Visible = false
                d.txt.Visible = false
            end
        end
    end
end)

makeTog("Cheater Detector", "Visuals", C.CheaterDetect, function(s) C.CheaterDetect = s end)
makeTog("Cheater Tracer (Oranye)", "Visuals", C.CheaterTracer, function(s) C.CheaterTracer = s end)
makeSlider("Speed Limit (studs/s)", "Visuals", 20, 120, C.CheaterSpeedLimit, function(v) C.CheaterSpeedLimit = v end)

-- ==================== END CHEATER DETECTOR ====================

-- ==================== CHEATER AURA + SPECTATE ====================
C.CheaterAura = false
C.CheaterAuraRange = 35
C.CheaterSpectate = false

local function cheaterIsFlagged(d)
    if not d or not d.reasons then return false end
    local now = os.clock()
    for _, expiry in pairs(d.reasons) do
        if expiry > now then return true end
    end
    return false
end

local function getFlaggedList()
    local list = {}
    if CD == nil then return list end
    for v, d in pairs(CD) do
        if v ~= LP and cheaterIsFlagged(d) then
            local char = v.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hrp and hum and hum.Health > 0 then
                table.insert(list, {plr = v, hrp = hrp, hum = hum, d = d})
            end
        end
    end
    return list
end

-- ---------- AURA KHUSUS CHEATER ----------
local auraLast = 0
R.RenderStepped:Connect(function()
    if not C.CheaterAura then return end
    local now = os.clock()
    if now - auraLast < 0.25 then return end
    auraLast = now
    local myChar = LP.Character
    local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
    local myHum = myChar and myChar:FindFirstChildOfClass("Humanoid")
    if not myHRP or not myHum or myHum.Health <= 0 then return end
    local best = nil
    local bestDist = C.CheaterAuraRange
    for _, info in ipairs(getFlaggedList()) do
        local dist = (info.hrp.Position - myHRP.Position).Magnitude
        if dist <= bestDist then
            bestDist = dist
            best = info
        end
    end
    if best then
        pcall(function()
            local tool = myChar:FindFirstChildOfClass("Tool")
            if not tool then
                local bpTool = LP:FindFirstChild("Backpack") and LP.Backpack:FindFirstChildOfClass("Tool")
                if bpTool then
                    myHum:EquipTool(bpTool)
                    tool = myChar:FindFirstChildOfClass("Tool")
                end
            end
            if tool then
                tool:Activate()
                local handle = tool:FindFirstChild("Handle")
                if handle and firetouchinterest then
                    firetouchinterest(handle, best.hrp, 0)
                    firetouchinterest(handle, best.hrp, 1)
                end
            end
        end)
    end
end)

-- ---------- AUTO SPECTATE CHEATER ----------
local specManual = nil
local specCurrent = nil

local function notifySpec(name)
    pcall(function()
        game:GetService("StarterGui"):SetCore("SendNotification", {
            Title = "Spectate Cheater",
            Text = "Nonton: " .. name .. " (B = next)",
            Duration = 3
        })
    end)
end

local function resetCamToSelf()
    pcall(function()
        local myHum = LP.Character and LP.Character:FindFirstChildOfClass("Humanoid")
        if myHum then
            W.CurrentCamera.CameraSubject = myHum
        end
    end)
end

U.InputBegan:Connect(function(inp, gpe)
    if gpe then return end
    if not C.CheaterSpectate then return end
    if inp.KeyCode ~= Enum.KeyCode.B then return end
    local list = getFlaggedList()
    if #list == 0 then return end
    local idx = 1
    if specCurrent then
        for i, info in ipairs(list) do
            if info.plr == specCurrent then
                idx = (i % #list) + 1
                break
            end
        end
    end
    specManual = list[idx].plr
    specCurrent = specManual
    W.CurrentCamera.CameraSubject = list[idx].hum
    notifySpec(specManual.Name)
end)

R.RenderStepped:Connect(function()
    if not C.CheaterSpectate then
        if specCurrent then
            specCurrent = nil
            specManual = nil
            resetCamToSelf()
        end
        return
    end
    local list = getFlaggedList()
    if #list == 0 then
        if specCurrent then
            specCurrent = nil
            specManual = nil
            resetCamToSelf()
        end
        return
    end
    local targetInfo = nil
    if specManual then
        for _, info in ipairs(list) do
            if info.plr == specManual then
                targetInfo = info
                break
            end
        end
        if not targetInfo then specManual = nil end
    end
    if not targetInfo then
        local myHRP = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        local bestDist = math.huge
        for _, info in ipairs(list) do
            local dist = 0
            if myHRP then dist = (info.hrp.Position - myHRP.Position).Magnitude end
            if dist < bestDist then
                bestDist = dist
                targetInfo = info
            end
        end
    end
    if targetInfo then
        if specCurrent ~= targetInfo.plr then
            specCurrent = targetInfo.plr
            notifySpec(specCurrent.Name)
        end
        pcall(function()
            W.CurrentCamera.CameraSubject = targetInfo.hum
        end)
    end
end)

makeTog("Aura Khusus Cheater", "Combat", C.CheaterAura, function(s) C.CheaterAura = s end)
makeSlider("Aura Range Cheater", "Combat", 10, 100, C.CheaterAuraRange, function(v) C.CheaterAuraRange = v end)
makeTog("Spectate Cheater (Auto)", "Visuals", C.CheaterSpectate, function(s)
    C.CheaterSpectate = s
    if not s then
        specManual = nil
        specCurrent = nil
        resetCamToSelf()
    end
end)

-- ==================== END CHEATER AURA + SPECTATE ====================

print("[MOMO HUB V30 + NEON] Loaded.")
