local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local Mouse = game.Players.LocalPlayer:GetMouse()

local Blacklist = {}
local BlacklistInput = {Enum.UserInputType.MouseMovement}

if CoreGui:FindFirstChild("Shaman") then
    CoreGui.Shaman:Destroy()
    CoreGui.Tooltips:Destroy()
end

local function CheckTable(table)
    local i = 0
    for _,v in pairs(table) do
        i = i + 1
    end
    return i
end

local TabSelected = nil
local EditOpened = false
local ColorElements = {}

task.spawn(function()
while true do
if EditOpened and CheckTable(ColorElements) > 0 then
local hue = tick() % 7 / 7
local color = Color3.fromHSV(hue, 1, 1)

for frame, v in pairs(ColorElements) do
    if v.Enabled then
        if frame.ClassName == "Frame" then
        frame.BackgroundColor3 = color
        else
        frame.ImageColor3 = color
        end
    end
end
end
wait()
end
end)

local library = {
    Flags = {},
    _flagSetters = {},
    ChangingKeybind = false
}

local request = syn and syn.request or http and http.request or http_request or request or httprequest

function library:GetXY(GuiObject)
    local Max, May = GuiObject.AbsoluteSize.X, GuiObject.AbsoluteSize.Y
    local Px, Py = math.clamp(Mouse.X - GuiObject.AbsolutePosition.X, 0, Max), math.clamp(Mouse.Y - GuiObject.AbsolutePosition.Y, 0, May)
    return Px/Max, Py/May
end

function library:Window(Info)
Info.Text = Info.Text or "Shaman"

local window = {}

local shamanScreenGui = Instance.new("ScreenGui")
shamanScreenGui.Name = "Shaman"
shamanScreenGui.Parent = CoreGui
library.ScreenGui = shamanScreenGui

local tooltipScreenGui = Instance.new("ScreenGui")
tooltipScreenGui.Name = "Tooltips"
tooltipScreenGui.Parent = CoreGui

local function Tooltip(text)
local tooltip = Instance.new("Frame")
tooltip.Name = "Tooltip"
tooltip.AnchorPoint = Vector2.new(0.5, 0)
tooltip.BackgroundColor3 = Color3.fromRGB(79, 79, 79)
tooltip.Visible = false
tooltip.Position = UDim2.new(0.352, 0, 0.0741, 0)
tooltip.Size = UDim2.new(0, 100, 0, 19)
tooltip.ZIndex = 5
tooltip.Parent = tooltipScreenGui

local newuICorner = Instance.new("UICorner")
newuICorner.Name = "UICorner"
newuICorner.CornerRadius = UDim.new(0, 3)
newuICorner.Parent = tooltip

local newuIStroke = Instance.new("UIStroke")
newuIStroke.Name = "UIStroke"
newuIStroke.Color = Color3.fromRGB(98, 98, 98)
newuIStroke.Parent = tooltip

local tooltipText = Instance.new("TextLabel")
tooltipText.Name = "TooltipText"
tooltipText.Font = Enum.Font.GothamBold
tooltipText.Text = text
tooltipText.TextColor3 = Color3.fromRGB(217, 217, 217)
tooltipText.TextSize = 11
tooltipText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
tooltipText.BackgroundTransparency = 1
tooltipText.Size = UDim2.new(0, 100, 0, 19)
tooltipText.Parent = tooltip
tooltipText.ZIndex = 6

local TextBounds = tooltipText.TextBounds

tooltip.Size = UDim2.new(0, TextBounds.X + 10, 0, 19)
tooltipText.Size = UDim2.new(0, TextBounds.X + 10, 0, 19)

return tooltip
end

local function AddTooltip(element, text)
    local Tooltip = Tooltip(text)
    local Hovered = false
    
    local function Update()
    local MousePos = UserInputService:GetMouseLocation()
    local Viewport = workspace.CurrentCamera.ViewportSize
    
    Tooltip.Position = UDim2.new(MousePos.X / Viewport.X, 0, MousePos.Y / Viewport.Y, 0) + UDim2.new(0,0,0,-43)
    end

    element.MouseEnter:Connect(function()
        Hovered = true
        wait(.5)
        if Hovered then
        Tooltip.Visible = true
        end
    end)
    
    element.MouseLeave:Connect(function()
        Hovered = false
        Tooltip.Visible = false
    end)
    
    element.MouseMoved:Connect(function()
        Update()
    end)
end

local main = Instance.new("Frame")
main.Name = "Main"
main.BackgroundColor3 = Color3.fromRGB(27, 27, 27)
main.BorderSizePixel = 0
main.ClipsDescendants = true
main.Position = UDim2.new(0.361, 0, 0.308, 0)
main.Size = UDim2.new(0, 450, 0, 321)
main.Parent = shamanScreenGui

local uICorner = Instance.new("UICorner")
uICorner.Name = "UICorner"
uICorner.CornerRadius = UDim.new(0, 5)
uICorner.Parent = main

local topbar = Instance.new("Frame")
topbar.Name = "Topbar"
topbar.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
topbar.Size = UDim2.new(0, 450, 0, 31)
topbar.Parent = main
topbar.ZIndex = 2

local dragging
local dragInput
local dragStart
local startPos

local function update(input)
    local delta = input.Position - dragStart
    main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

topbar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = main.Position
                
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

topbar.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)


local uICorner1 = Instance.new("UICorner")
uICorner1.Name = "UICorner"
uICorner1.Parent = topbar

local frame = Instance.new("Frame")
frame.Name = "Frame"
frame.BackgroundColor3 = Color3.fromRGB(24, 24, 24)
frame.BorderSizePixel = 0
frame.Position = UDim2.new(0, 0, 0.625, 0)
frame.Size = UDim2.new(0, 450, 0, 11)
frame.Parent = topbar

local frame1 = Instance.new("Frame")
frame1.Name = "Frame"
frame1.AnchorPoint = Vector2.new(0.5, 1)
frame1.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
frame1.BorderSizePixel = 0
frame1.Position = UDim2.new(0.5, 0, 1, 0)
frame1.Size = UDim2.new(0, 450, 0, 1)
frame1.ZIndex = 2
frame1.Parent = frame

local uIGradient = Instance.new("UIGradient")
uIGradient.Name = "UIGradient"
uIGradient.Color = ColorSequence.new({
  ColorSequenceKeypoint.new(0, Color3.fromRGB(183, 248, 219)),
  ColorSequenceKeypoint.new(1, Color3.fromRGB(80, 167, 194)),
})
uIGradient.Enabled = false
uIGradient.Parent = frame1

local textLabel = Instance.new("TextLabel")
textLabel.Name = "TextLabel"
textLabel.Font = Enum.Font.GothamBold
textLabel.Text = ""
textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
textLabel.TextSize = 12
textLabel.TextXAlignment = Enum.TextXAlignment.Left
textLabel.BackgroundColor3 = Color3.fromRGB(237, 237, 237)
textLabel.BackgroundTransparency = 1
textLabel.Position = UDim2.new(0.015, 0, 0, 0)
textLabel.Size = UDim2.new(0, 120, 0, 30)
textLabel.ZIndex = 2
textLabel.Parent = topbar

task.spawn(function()
    local titleText = Info.Text or "EndorisFTAP Reborn"
    local typeSpeed = 0.07
    local deleteSpeed = 0.03
    local holdFull = 3
    local holdEmpty = 3

    while true do
        for i = 1, #titleText do
            textLabel.Text = titleText:sub(1, i) .. "|"
            task.wait(typeSpeed)
        end
        for t = 1, 6 do
            textLabel.Text = titleText .. (t % 2 == 1 and "|" or " ")
            task.wait(holdFull / 6)
        end
        for i = #titleText, 0, -1 do
            textLabel.Text = titleText:sub(1, i) .. "|"
            task.wait(deleteSpeed)
        end
        for t = 1, 6 do
            textLabel.Text = (t % 2 == 1 and "|" or " ")
            task.wait(holdEmpty / 6)
        end
    end
end)

local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Text = "×"
closeButton.TextColor3 = Color3.fromRGB(237, 237, 237)
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 21
closeButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
closeButton.BackgroundTransparency = 1
closeButton.Position = UDim2.new(0.947, 0, 0.08, 0)
closeButton.Size = UDim2.new(0, 22, 0, 22)
closeButton.ZIndex = 2
closeButton.Parent = topbar

closeButton.MouseButton1Click:Connect(function()
    shamanScreenGui.Enabled = false
end)

closeButton.MouseEnter:Connect(function()
    TweenService:Create(closeButton, TweenInfo.new(.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(217, 97, 99)}):Play()
end)

closeButton.MouseLeave:Connect(function()
    TweenService:Create(closeButton, TweenInfo.new(.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(237, 237, 237)}):Play()
end)

closeButton.MouseLeave:Connect(function()
    TweenService:Create(closeButton, TweenInfo.new(.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(217, 217, 217)}):Play()
end)

local minimizeButton = Instance.new("ImageButton")
minimizeButton.Name = "MinimizeButton"
minimizeButton.Image = "rbxassetid://10664064072"
minimizeButton.ImageColor3 = Color3.fromRGB(237, 237, 237)
minimizeButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.BackgroundTransparency = 1
minimizeButton.Position = UDim2.new(0.893, 0, 0.155, 0)
minimizeButton.Size = UDim2.new(0, 17, 0, 17)
minimizeButton.ZIndex = 2
minimizeButton.Parent = topbar

minimizeButton.MouseEnter:Connect(function()
    TweenService:Create(minimizeButton, TweenInfo.new(.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {ImageColor3 = Color3.fromRGB(194, 162, 76)}):Play()
end)

minimizeButton.MouseLeave:Connect(function()
    TweenService:Create(minimizeButton, TweenInfo.new(.1, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {ImageColor3 = Color3.fromRGB(217, 217, 217)}):Play()
end)

local Opened = true

minimizeButton.MouseButton1Click:Connect(function()
    Opened = not Opened
    
    topbar.Frame.Visible = Opened
    task.spawn(function()
    if Opened then
        wait(.15)
    end
    for _,v in pairs(main:GetChildren()) do
        if v.Name == "TabContainer" then
            v.Visible = Opened
        end
    end
    for _,v in pairs(main:GetChildren()) do
       if v.Name == "LeftContainer" or v.Name == "RightContainer" and v.Visible then
           v.Size = Opened and UDim2.new(0, 168,0, 287) or UDim2.new(0, 168,0, 0)
       end
    end
    end)
    
    TweenService:Create(main, TweenInfo.new(.2, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = Opened and UDim2.new(0, 450,0, 321) or UDim2.new(0, 450,0, 30)}):Play()
end)

local editButton = Instance.new("TextButton")
editButton.Name = "EditButton"
editButton.Text = ""
editButton.BackgroundColor3 = Color3.fromRGB(237, 237, 237)
editButton.BackgroundTransparency = 0
editButton.Position = UDim2.new(0.841, 0, 0.226, 0)
editButton.Size = UDim2.new(0, 15, 0, 15)
editButton.ZIndex = 2
editButton.Parent = topbar
Instance.new("UICorner", editButton).CornerRadius = UDim.new(1, 0)

local uiGradient = Instance.new("UIGradient")
uiGradient.Name = "UIGradient"
uiGradient.Enabled = false
uiGradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0,Color3.fromRGB(255,0,0)),
    ColorSequenceKeypoint.new(0.2,Color3.fromRGB(255,255,0)),
    ColorSequenceKeypoint.new(0.4,Color3.fromRGB(0,255,0)),
    ColorSequenceKeypoint.new(0.6,Color3.fromRGB(0,255,255)),
    ColorSequenceKeypoint.new(0.8,Color3.fromRGB(0,0,255)),
    ColorSequenceKeypoint.new(1,Color3.fromRGB(255,0,255)),
}
uiGradient.Parent = editButton

task.spawn(function()
    while wait() do -- skidded from devforum
    if uiGradient.Enabled then
    local loop = tick() % 2 / 2
    colors = {}
    for i = 1, 7 + 1, 1 do
        z = Color3.fromHSV(loop - ((i - 1)/7), 1, 1)
        if loop - ((i - 1) / 7) < 0 then
            z = Color3.fromHSV((loop - ((i - 1) / 7)) + 1, 1, 1)
        end
        local d = ColorSequenceKeypoint.new((i - 1) / 7, z)
        table.insert(colors, #colors + 1, d)
    end
    uiGradient.Color = ColorSequence.new(colors)
end
end
end)

editButton.MouseEnter:Connect(function()
    if not EditOpened then
        uiGradient.Enabled = true
    end
end)

editButton.MouseLeave:Connect(function()
    if not EditOpened then
        uiGradient.Enabled = false
    end
end)

editButton.MouseButton1Click:Connect(function()
    EditOpened = not EditOpened
    
    uiGradient.Enabled = EditOpened and true or false
    
    if not EditOpened then
        for frame, v in pairs(ColorElements) do
            if v.Enabled then
                if frame.ClassName == "Frame" then
                TweenService:Create(frame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(48, 207, 106)}):Play()
                else
                TweenService:Create(frame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {ImageColor3 = Color3.fromRGB(48, 207, 106)}):Play()
                end
            end
        end
    else
        for _,v in pairs(ColorElements) do
            if v.Type ~= "Toggle" then
                v.Enabled = true
            end
        end
    end
end)

local tabContainer = Instance.new("Frame")
tabContainer.Name = "TabContainer"
tabContainer.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
tabContainer.Position = UDim2.new(0, 0, 0.0935, 0)
tabContainer.Size = UDim2.new(0, 114, 0, 291)
tabContainer.Parent = main

local uICorner2 = Instance.new("UICorner")
uICorner2.Name = "UICorner"
uICorner2.CornerRadius = UDim.new(0, 5)
uICorner2.Parent = tabContainer

local fix = Instance.new("Frame")
fix.Name = "Fix"
fix.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
fix.BorderSizePixel = 0
fix.Position = UDim2.new(0.895, 0, 0, 0)
fix.Size = UDim2.new(0, 11, 0, 285)
fix.Parent = tabContainer

local fix1 = Instance.new("Frame")
fix1.Name = "Fix"
fix1.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
fix1.BorderSizePixel = 0
fix1.Position = UDim2.new(0, 0, -0.00351, 0)
fix1.Size = UDim2.new(0, 11, 0, 79)
fix1.Parent = tabContainer

local scrollingContainer = Instance.new("ScrollingFrame")
scrollingContainer.Name = "ScrollingContainer"
scrollingContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
scrollingContainer.CanvasSize = UDim2.new()
scrollingContainer.ScrollBarImageColor3 = Color3.fromRGB(56, 56, 56)
scrollingContainer.ScrollBarThickness = 2
scrollingContainer.Active = true
scrollingContainer.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
scrollingContainer.BackgroundTransparency = 1
scrollingContainer.BorderSizePixel = 0
scrollingContainer.Size = UDim2.new(0, 114, 0, 285)
scrollingContainer.ZIndex = 2
scrollingContainer.Parent = tabContainer

function window:Tab(Info)
Info.Text = Info.Text or "Tab"

local tab = {}

local tabButton = Instance.new("Frame")
tabButton.Name = "TabButton"
tabButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
tabButton.BackgroundTransparency = 1
tabButton.Size = UDim2.new(0, 113, 0, 27)
tabButton.Parent = scrollingContainer

local tabFrame = Instance.new("Frame")
tabFrame.Name = "TabFrame"
tabFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
tabFrame.BackgroundTransparency = 0.96
tabFrame.BorderSizePixel = 0
tabFrame.Position = UDim2.new(0.067, -5, 0.013, 3)
tabFrame.Size = UDim2.new(0, 107, 0, 23)
tabFrame.ZIndex = 2
tabFrame.Parent = tabButton

tabFrame.MouseEnter:Connect(function()
    if TabSelected ~= tabFrame or TabSelected == nil then
        TweenService:Create(tabFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = .93}):Play()
    end
end)

tabFrame.MouseLeave:Connect(function()
    if TabSelected ~= tabFrame or TabSelected == nil then
        TweenService:Create(tabFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = .96}):Play()
    end
end)

local tabTextButton = Instance.new("TextButton")
tabTextButton.Name = "TabTextButton"
tabTextButton.Font = Enum.Font.SourceSans
tabTextButton.Text = ""
tabTextButton.TextColor3 = Color3.fromRGB(0, 0, 0)
tabTextButton.TextSize = 14
tabTextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
tabTextButton.BackgroundTransparency = 1
tabTextButton.Size = UDim2.new(0, 107, 0, 23)
tabTextButton.Parent = tabFrame

local uICorner3 = Instance.new("UICorner")
uICorner3.Name = "UICorner"
uICorner3.CornerRadius = UDim.new(0, 3)
uICorner3.Parent = tabFrame

local textLabel1 = Instance.new("TextLabel")
textLabel1.Name = "TextLabel"
textLabel1.Font = Enum.Font.GothamBold
textLabel1.Text = Info.Text
textLabel1.TextColor3 = Color3.fromRGB(237, 237, 237)
textLabel1.TextSize = 11
textLabel1.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
textLabel1.BackgroundTransparency = 1
textLabel1.Size = UDim2.new(0, 108, 0, 23)
textLabel1.ZIndex = 2
textLabel1.Parent = tabFrame

local uIStroke = Instance.new("UIStroke")
uIStroke.Name = "UIStroke"
uIStroke.Color = Color3.fromRGB(68, 68, 68) -- 183, 248, 219
uIStroke.Transparency = 0.45
uIStroke.Parent = tabFrame

local selected = Instance.new("Frame")
selected.Name = "Selected"
selected.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
selected.BackgroundTransparency = 0.1
selected.Visible = false
selected.BorderSizePixel = 0
selected.Position = UDim2.new(0.067, -5, 0.013, 3)
selected.Size = UDim2.new(0, 108, 0, 23)
selected.Parent = tabButton

local uICorner4 = Instance.new("UICorner")
uICorner4.Name = "UICorner"
uICorner4.CornerRadius = UDim.new(0, 3)
uICorner4.Parent = selected

local uIGradient1 = Instance.new("UIGradient")
uIGradient1.Name = "UIGradient"
uIGradient1.Parent = selected
uIGradient1.Color = ColorSequence.new({
  ColorSequenceKeypoint.new(0, Color3.fromRGB(183, 248, 219)),
  ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 25, 25)),
})
uIGradient1.Transparency = NumberSequence.new({
  NumberSequenceKeypoint.new(0, 0.5),
  NumberSequenceKeypoint.new(0.688, 0.725),
  NumberSequenceKeypoint.new(1, 0.506),
})

local leftContainer = Instance.new("ScrollingFrame")
leftContainer.Name = "LeftContainer"
leftContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
leftContainer.CanvasSize = UDim2.new()
leftContainer.ScrollBarThickness = 2
leftContainer.ScrollBarImageColor3 = Color3.fromRGB(56, 56, 56)
leftContainer.BackgroundColor3 = Color3.fromRGB(26, 26, 26)
leftContainer.BorderSizePixel = 0
leftContainer.Position = UDim2.new(0.253, 0, 0.0935, 0)
leftContainer.Selectable = false
leftContainer.Size = UDim2.new(0, 168, 0, 287)
leftContainer.Parent = main
leftContainer.Visible = false

local uIListLayout2 = Instance.new("UIListLayout")
uIListLayout2.Name = "UIListLayout"
uIListLayout2.SortOrder = Enum.SortOrder.LayoutOrder
uIListLayout2.Parent = leftContainer

local uIPadding2 = Instance.new("UIPadding")
uIPadding2.Name = "UIPadding"
uIPadding2.PaddingLeft = UDim.new(0, 4)
uIPadding2.PaddingRight = UDim.new(0, 4)
uIPadding2.PaddingTop = UDim.new(0, 3)
uIPadding2.PaddingBottom = UDim.new(0, 3)
uIPadding2.Parent = leftContainer

local rightContainer = Instance.new("ScrollingFrame")
rightContainer.Name = "RightContainer"
rightContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
rightContainer.CanvasSize = UDim2.new()
rightContainer.ScrollBarThickness = 2
rightContainer.ScrollBarImageColor3 = Color3.fromRGB(56, 56, 56)
rightContainer.BackgroundColor3 = Color3.fromRGB(26, 26, 26)
rightContainer.BorderSizePixel = 0
rightContainer.Position = UDim2.new(0.627, 0, 0.0935, 0)
rightContainer.Selectable = false
rightContainer.Size = UDim2.new(0, 168, 0, 287)
rightContainer.Parent = main
rightContainer.Visible = false

local uIListLayout3 = Instance.new("UIListLayout")
uIListLayout3.Name = "UIListLayout"
uIListLayout3.SortOrder = Enum.SortOrder.LayoutOrder
uIListLayout3.Parent = rightContainer

local uIPadding3 = Instance.new("UIPadding")
uIPadding3.Name = "UIPadding"
uIPadding3.PaddingLeft = UDim.new(0, 4)
uIPadding3.PaddingRight = UDim.new(0, 4)
uIPadding3.PaddingTop = UDim.new(0, 3)
uIPadding3.PaddingBottom = UDim.new(0, 3)
uIPadding3.Parent = rightContainer

local uICorner8 = Instance.new("UICorner")
uICorner8.Name = "UICorner"
uICorner8.CornerRadius = UDim.new(0, 3)
uICorner8.Parent = rightContainer

function tab:Section(Info)
Info.Text = Info.Text or "Section"
Info.Side = Info.Side or "Left"

local sectiontable = {}

local Side
    
if Info.Side == "Left" then
    Side = leftContainer
    else
    Side = rightContainer
end
    
local section = Instance.new("Frame")
section.Name = "Section"
section.BackgroundColor3 = Color3.fromRGB(42, 42, 42)
section.BackgroundTransparency = 1
section.Size = UDim2.new(1, 0, 0, 27)
section.Parent = Side

local Closed = Instance.new("BoolValue", section)
Closed.Value = false

local sectionFrame = Instance.new("Frame")
sectionFrame.Name = "SectionFrame"
sectionFrame.BackgroundColor3 = Color3.fromRGB(42, 42, 42)
sectionFrame.ClipsDescendants = false
sectionFrame.Size = UDim2.new(1, 0, 0, 23)
sectionFrame.Parent = section

sectionFrame.ChildAdded:Connect(function(v)
    local contentH = 0
    local childCount = 0
    for _, child in sectionFrame:GetChildren() do
        if child:IsA("Frame") then
            contentH = contentH + child.Size.Y.Offset
            childCount = childCount + 1
        end
    end
    local layout = sectionFrame:FindFirstChildOfClass("UIListLayout")
    local layoutPadding = layout and layout.Padding.Offset or 0
    local gapsH = math.max(0, childCount - 1) * layoutPadding
    local frameH = 23 + contentH + gapsH + 3
    section.Size = UDim2.new(1, 0, 0, frameH + 6)
    sectionFrame.Size = UDim2.new(1, 0, 0, frameH)
end)

local uIStroke3 = Instance.new("UIStroke")
uIStroke3.Name = "UIStroke"
uIStroke3.Color = Color3.fromRGB(52, 52, 52)
uIStroke3.Parent = sectionFrame

local uICorner7 = Instance.new("UICorner")
uICorner7.Name = "UICorner"
uICorner7.CornerRadius = UDim.new(0, 3)
uICorner7.Parent = sectionFrame

local uIListLayout1 = Instance.new("UIListLayout")
uIListLayout1.Name = "UIListLayout"
uIListLayout1.SortOrder = Enum.SortOrder.LayoutOrder
uIListLayout1.Padding = UDim.new(0, 2)
uIListLayout1.Parent = sectionFrame

local uIPadding1 = Instance.new("UIPadding")
uIPadding1.Name = "UIPadding"
uIPadding1.PaddingTop = UDim.new(0, 23)
uIPadding1.PaddingLeft = UDim.new(0, 3)
uIPadding1.PaddingRight = UDim.new(0, 3)
uIPadding1.PaddingBottom = UDim.new(0, 3)
uIPadding1.Parent = sectionFrame

local sectionName = Instance.new("TextLabel")
sectionName.Name = "SectionName"
sectionName.Font = Enum.Font.GothamBold
sectionName.Text = Info.Text
sectionName.TextColor3 = Color3.fromRGB(217, 217, 217)
sectionName.TextSize = 11
sectionName.TextXAlignment = Enum.TextXAlignment.Left
sectionName.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sectionName.BackgroundTransparency = 1
sectionName.Position = UDim2.new(0.0488, 0, 0, 0)
sectionName.Size = UDim2.new(0, 128, 0, 23)
sectionName.Parent = section

local sectionButton = Instance.new("TextButton")
sectionButton.Name = "SectionButton"
sectionButton.Font = Enum.Font.SourceSans
sectionButton.Text = ""
sectionButton.TextColor3 = Color3.fromRGB(0, 0, 0)
sectionButton.TextSize = 14
sectionButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sectionButton.BackgroundTransparency = 1
sectionButton.Size = UDim2.new(1, 0, 0, 23)
sectionButton.ZIndex = 2
sectionButton.Parent = section

local sectionIcon = Instance.new("ImageButton")
sectionIcon.Name = "SectionButton"
sectionIcon.Image = "rbxassetid://10664195729"
sectionIcon.ImageColor3 = Color3.fromRGB(217, 217, 217)
sectionIcon.AnchorPoint = Vector2.new(1, 0)
sectionIcon.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sectionIcon.BackgroundTransparency = 1
sectionIcon.Position = UDim2.new(1, -5, 0, 5)
sectionIcon.Size = UDim2.new(0, 13, 0, 13)
sectionIcon.ZIndex = 1
sectionIcon.Parent = section

sectionButton.Active = false
sectionButton.Visible = false
sectionIcon.Visible = false

function sectiontable:Label(Info)
Info.Text = Info.Text or "Label"
Info.Color = Info.Color or Color3.fromRGB(217, 217, 217)
Info.Tooltip = Info.Tooltip or ""

local insidelabel = {}

local label = Instance.new("Frame")
label.Name = "Label"
label.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
label.BackgroundTransparency = 1
label.Size = UDim2.new(1, 0, 0, 27)
label.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(label, Info.Tooltip)
end

local labelText = Instance.new("TextLabel")
labelText.Name = "LabelText"
labelText.Font = Enum.Font.GothamBold
labelText.TextColor3 = Info.Color
labelText.Text = Info.Text
labelText.TextSize = 11
labelText.TextXAlignment = Enum.TextXAlignment.Left
labelText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
labelText.BackgroundTransparency = 1
labelText.Position = UDim2.new(0.0488, 0, 0, 0)
labelText.Size = UDim2.new(1, 0, 0, 27)
labelText.Parent = label

function insidelabel:Set(SetInfo)
SetInfo.Text = SetInfo.Text or labelText.Text
SetInfo.Color = SetInfo.Color or labelText.TextColor3

labelText.Text = SetInfo.Text
labelText.TextColor3 = SetInfo.Color
end

return insidelabel
end

function sectiontable:Keybind(Info)
Info.Text = Info.Text or "Keybind"
Info.Default = Info.Default or Enum.KeyCode.Unknown
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""
Info.Mode = Info.Mode or "Toggle"

local PressKey = Info.Default
local PressInputType = nil
local Mode = Info.Mode
local Holding = false

if Info.Flag ~= nil then
    library.Flags[Info.Flag] = {Key = PressKey == Enum.KeyCode.Unknown and "" or PressKey.Name, Mode = Mode}
end

local keybind = Instance.new("Frame")
keybind.Name = "Keybind"
keybind.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
keybind.BackgroundTransparency = 1
keybind.Size = UDim2.new(1, 0, 0, 27)
keybind.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(keybind, Info.Tooltip)
end

local keybindText = Instance.new("TextLabel")
keybindText.Name = "KeybindText"
keybindText.Font = Enum.Font.GothamBold
keybindText.Text = Info.Text
keybindText.TextColor3 = Color3.fromRGB(217, 217, 217)
keybindText.TextSize = 11
keybindText.TextXAlignment = Enum.TextXAlignment.Left
keybindText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
keybindText.BackgroundTransparency = 1
keybindText.Position = UDim2.new(0.05, 0, 0, 0)
keybindText.Size = UDim2.new(1, 0, 0, 27)
keybindText.Parent = keybind

local keybindFrame = Instance.new("Frame")
keybindFrame.Name = "KeybindFrame"
keybindFrame.BackgroundColor3 = Color3.fromRGB(68, 68, 68)
keybindFrame.AnchorPoint = Vector2.new(1, 0)
keybindFrame.BorderSizePixel = 0
keybindFrame.Position = UDim2.new(1, -5, 0, 6)
keybindFrame.Size = UDim2.new(0, 30, 0, 15)
keybindFrame.Parent = keybind

local keybindUICorner = Instance.new("UICorner")
keybindUICorner.Name = "KeybindUICorner"
keybindUICorner.CornerRadius = UDim.new(0, 3)
keybindUICorner.Parent = keybindFrame

local keybindFrameText = Instance.new("TextLabel")
keybindFrameText.Name = "KeybindFrameText"
keybindFrameText.Font = Enum.Font.GothamBold
keybindFrameText.Text = PressKey == Enum.KeyCode.Unknown and "" or PressKey.Name
keybindFrameText.TextColor3 = Color3.fromRGB(217, 217, 217)
keybindFrameText.TextSize = 11
keybindFrameText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
keybindFrameText.BackgroundTransparency = 1
keybindFrameText.Size = UDim2.new(1, 0, 0, 15)
keybindFrameText.Parent = keybindFrame

local keybindButton = Instance.new("TextButton")
keybindButton.Name = "KeybindButton"
keybindButton.Font = Enum.Font.SourceSans
keybindButton.Text = ""
keybindButton.TextColor3 = Color3.fromRGB(0, 0, 0)
keybindButton.TextSize = 14
keybindButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
keybindButton.BackgroundTransparency = 1
keybindButton.Size = UDim2.new(1, 0, 0, 15)
keybindButton.Parent = keybindFrame

local keybindUIStroke = Instance.new("UIStroke")
keybindUIStroke.Name = "KeybindUIStroke"
keybindUIStroke.Color = Color3.fromRGB(84, 84, 84)
keybindUIStroke.Parent = keybindFrame

local TextBounds = keybindFrameText.TextBounds

keybindFrame.Size = UDim2.new(0, TextBounds.X + 10, 0, 15)

keybindFrameText:GetPropertyChangedSignal("Text"):Connect(function()
    TextBounds = keybindFrameText.TextBounds
    keybindFrame.Size = UDim2.new(0, TextBounds.X + 10, 0, 15)
end)

local function createModeMenu()
    local existing = library.ScreenGui:FindFirstChild("ModeMenu_"..tostring(Info.Flag or ""))
    if existing then existing:Destroy() end

    local absPos = keybindFrame.AbsolutePosition
    local absSize = keybindFrame.AbsoluteSize

    local menuFrame = Instance.new("Frame")
    menuFrame.Name = "ModeMenu_"..tostring(Info.Flag or "")
    menuFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    menuFrame.BorderSizePixel = 0
    menuFrame.Position = UDim2.new(0, absPos.X + absSize.X - 60, 0, absPos.Y + absSize.Y + 2)
    menuFrame.ZIndex = 9999
    menuFrame.Parent = library.ScreenGui

    local menuCorner = Instance.new("UICorner")
    menuCorner.CornerRadius = UDim.new(0, 4)
    menuCorner.Parent = menuFrame

    local menuStroke = Instance.new("UIStroke")
    menuStroke.Color = Color3.fromRGB(60, 60, 60)
    menuStroke.Parent = menuFrame

    local menuLayout = Instance.new("UIListLayout")
    menuLayout.SortOrder = Enum.SortOrder.LayoutOrder
    menuLayout.Padding = UDim.new(0, 1)
    menuLayout.Parent = menuFrame

    local function makeOption(text, order)
        local optBtn = Instance.new("TextButton")
        optBtn.Name = text
        optBtn.Font = Enum.Font.GothamBold
        optBtn.Text = text
        optBtn.TextColor3 = Mode == text and Color3.fromRGB(100, 180, 255) or Color3.fromRGB(180, 180, 180)
        optBtn.TextSize = 10
        optBtn.BackgroundColor3 = Mode == text and Color3.fromRGB(40, 40, 50) or Color3.fromRGB(35, 35, 35)
        optBtn.BackgroundTransparency = 0
        optBtn.Size = UDim2.new(0, 60, 0, 20)
        optBtn.LayoutOrder = order
        optBtn.ZIndex = 10000
        optBtn.Parent = menuFrame

        local optCorner = Instance.new("UICorner")
        optCorner.CornerRadius = UDim.new(0, 3)
        optCorner.Parent = optBtn

        optBtn.MouseButton1Click:Connect(function()
            Mode = text
            if Info.Flag ~= nil then
                local keyName = ""
                if PressInputType then
                    keyName = PressInputType.Name
                elseif PressKey ~= Enum.KeyCode.Unknown then
                    keyName = PressKey.Name
                end
                library.Flags[Info.Flag] = {Key = keyName, Mode = Mode}
            end
            menuFrame:Destroy()
        end)

        optBtn.MouseEnter:Connect(function()
            if Mode ~= text then
                optBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
            end
        end)
        optBtn.MouseLeave:Connect(function()
            if Mode ~= text then
                optBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            end
        end)
    end

    makeOption("Toggle", 1)
    makeOption("Hold", 2)

    local totalH = 20 * 2 + 1
    menuFrame.Size = UDim2.new(0, 60, 0, totalH)

    task.defer(function()
        local conn
        conn = UserInputService.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.MouseButton2 then
                if menuFrame and menuFrame.Parent then
                    local absPos = menuFrame.AbsolutePosition
                    local absSize = menuFrame.AbsoluteSize
                    local pos = input.Position
                    local insideMenu = pos.X >= absPos.X and pos.X <= absPos.X + absSize.X and pos.Y >= absPos.Y and pos.Y <= absPos.Y + absSize.Y
                    if not insideMenu then
                        menuFrame:Destroy()
                        if conn then conn:Disconnect() end
                    end
                else
                    if conn then conn:Disconnect() end
                end
            end
        end)
    end)
end

local KeybindConnection
local Changing = false
local JustClicked = false

keybindButton.MouseButton1Click:Connect(function()
    if KeybindConnection then KeybindConnection:Disconnect() end
    library.ChangingKeybind = false
    Changing = false
    JustClicked = true
    task.delay(0, function() JustClicked = false end)
    Changing = true
    library.ChangingKeybind = true
    task.delay(2, function()
        if library.ChangingKeybind then
            library.ChangingKeybind = false
            Changing = false
        end
    end)
    keybindFrameText.Text = "..."
    KeybindConnection = UserInputService.InputBegan:Connect(function(Key, gameProcessed)
        if JustClicked then return end
        if Key.UserInputType == Enum.UserInputType.MouseButton1 then return end
        if Key.UserInputType == Enum.UserInputType.MouseMovement then return end
        KeybindConnection:Disconnect()
        pcall(function()
            if Key.KeyCode == Enum.KeyCode.Escape then
                keybindFrameText.Text = ""
                PressKey = Enum.KeyCode.Unknown
                PressInputType = nil
                if Info.Flag ~= nil then
                    library.Flags[Info.Flag] = {Key = "", Mode = Mode}
                end
            elseif Key.UserInputType ~= Enum.UserInputType.Keyboard then
                local typeName = Key.UserInputType.Name
                keybindFrameText.Text = typeName
                PressKey = Enum.KeyCode.Unknown
                PressInputType = Key.UserInputType
                if Info.Flag ~= nil then
                    library.Flags[Info.Flag] = {Key = typeName, Mode = Mode}
                end
            elseif Key.KeyCode ~= Enum.KeyCode.Unknown then
                keybindFrameText.Text = Key.KeyCode.Name
                PressKey = Key.KeyCode
                PressInputType = nil
                if Info.Flag ~= nil then
                    library.Flags[Info.Flag] = {Key = Key.KeyCode.Name, Mode = Mode}
                end
            end
        end)
        task.delay(0.15, function()
            Changing = false
            library.ChangingKeybind = false
        end)
    end)
end)

keybindButton.MouseButton2Click:Connect(function()
    createModeMenu()
end)

UserInputService.InputBegan:Connect(function(Key, gameProcessed)
    if Changing then return end
    if library.ChangingKeybind then return end
    if gameProcessed and not Info.BypassGameProcessed then return end
    local matched = false
    if PressInputType and Key.UserInputType == PressInputType then
        matched = true
    elseif PressKey ~= Enum.KeyCode.Unknown and Key.KeyCode == PressKey then
        matched = true
    end
    if matched then
        if Mode == "Toggle" then
            Holding = not Holding
            task.spawn(function()
                pcall(Info.Callback, Holding)
            end)
        elseif Mode == "Hold" then
            Holding = true
            task.spawn(function()
                pcall(Info.Callback, true)
            end)
        end
    end
end)

UserInputService.InputEnded:Connect(function(Key, gameProcessed)
    local matched = false
    if PressInputType and Key.UserInputType == PressInputType then
        matched = true
    elseif PressKey ~= Enum.KeyCode.Unknown and Key.KeyCode == PressKey then
        matched = true
    end
    if matched and Mode == "Hold" and Holding then
        Holding = false
        task.spawn(function()
            pcall(Info.Callback, false)
        end)
    end
end)

if Info.Flag ~= nil then
    library._flagSetters[Info.Flag] = function(v)
        if type(v) == "table" then
            local newKey = v.Key or ""
            local newMode = v.Mode or Mode
            if newKey ~= "" then
                local ok, keyCode = pcall(function() return Enum.KeyCode[newKey] end)
                if ok and keyCode and keyCode ~= Enum.KeyCode.Unknown then
                    PressKey = keyCode
                    PressInputType = nil
                    keybindFrameText.Text = keyCode.Name
                else
                    local ok2, inputType = pcall(function() return Enum.UserInputType[newKey] end)
                    if ok2 and inputType then
                        PressKey = Enum.KeyCode.Unknown
                        PressInputType = inputType
                        keybindFrameText.Text = inputType.Name
                    end
                end
            else
                PressKey = Enum.KeyCode.Unknown
                PressInputType = nil
                keybindFrameText.Text = ""
            end
            Mode = newMode
            library.Flags[Info.Flag] = {Key = newKey, Mode = Mode}
        end
    end
end

end

function sectiontable:Button(Info)
Info.Text = Info.Text or "Button"
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""

local buttontable = {}
    
local button = Instance.new("Frame")
button.Name = "Button"
button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
button.BackgroundTransparency = 0
button.Size = UDim2.new(1, 0, 0, 27)
button.Parent = sectionFrame
Instance.new("UICorner", button).CornerRadius = UDim.new(0, 3)
local buttonStroke = Instance.new("UIStroke")
buttonStroke.Color = Color3.fromRGB(55, 55, 55)
buttonStroke.Thickness = 1
buttonStroke.Parent = button

if Info.Tooltip ~= "" then
    AddTooltip(button, Info.Tooltip)
end

local buttonText = Instance.new("TextLabel")
buttonText.Name = "ButtonText"
buttonText.Font = Enum.Font.GothamBold
buttonText.Text = Info.Text
buttonText.TextColor3 = Color3.fromRGB(217, 217, 217)
buttonText.TextSize = 11
buttonText.TextXAlignment = Enum.TextXAlignment.Left
buttonText.TextYAlignment = Enum.TextYAlignment.Center
buttonText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
buttonText.BackgroundTransparency = 1
buttonText.Position = UDim2.new(0.0488, 0, 0, 0)
buttonText.Size = UDim2.new(1, 0, 1, 0)
buttonText.Parent = button

local textButton = Instance.new("TextButton")
textButton.Name = "TextButton"
textButton.Font = Enum.Font.SourceSans
textButton.Text = ""
textButton.TextColor3 = Color3.fromRGB(0, 0, 0)
textButton.TextSize = 14
textButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
textButton.BackgroundTransparency = 1
textButton.Size = UDim2.new(1, 0, 0, 27)
textButton.Parent = button

textButton.MouseButton1Click:Connect(function()
    task.spawn(function()
        pcall(Info.Callback)
    end)
end)
end

function sectiontable:Input(Info)
Info.Placeholder = Info.Placeholder or "Input"
Info.Flag = Info.Flag or nil
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""

local input = Instance.new("Frame")
input.Name = "Input"
input.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
input.BackgroundTransparency = 1
input.Size = UDim2.new(1, 0, 0, 27)
input.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(input, Info.Tooltip)
end

local inputFrame = Instance.new("Frame")
inputFrame.Name = "InputFrame"
inputFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
inputFrame.BackgroundTransparency = 1
inputFrame.Size = UDim2.new(1, 0, 0, 27)
inputFrame.Parent = input

local inputOuter = Instance.new("Frame")
inputOuter.Name = "InputOuter"
inputOuter.AnchorPoint = Vector2.new(0.5, 0.5)
inputOuter.BackgroundColor3 = Color3.fromRGB(68, 68, 68)
inputOuter.BorderSizePixel = 0
inputOuter.ClipsDescendants = true
inputOuter.Position = UDim2.new(0.5, 0, 0.5, 0)
inputOuter.Size = UDim2.new(0.95, 0, 0, 21)
inputOuter.Parent = inputFrame

local inputUICorner = Instance.new("UICorner")
inputUICorner.Name = "InputUICorner"
inputUICorner.CornerRadius = UDim.new(0, 3)
inputUICorner.Parent = inputOuter

local inputUIStroke = Instance.new("UIStroke")
inputUIStroke.Name = "InputUIStroke"
inputUIStroke.Color = Color3.fromRGB(84, 84, 84)
inputUIStroke.Parent = inputOuter

local inputTextBox = Instance.new("TextBox")
inputTextBox.Name = "InputTextBox"
inputTextBox.CursorPosition = -1
inputTextBox.Font = Enum.Font.GothamBold
inputTextBox.PlaceholderColor3 = Color3.fromRGB(217, 217, 217)
inputTextBox.PlaceholderText = Info.Placeholder
inputTextBox.Text = ""
inputTextBox.TextColor3 = Color3.fromRGB(237, 237, 237)
inputTextBox.TextSize = 11
inputTextBox.TextXAlignment = Enum.TextXAlignment.Left
inputTextBox.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
inputTextBox.BackgroundTransparency = 1
inputTextBox.Position = UDim2.new(0.0253, 0, 0, 0)
inputTextBox.Size = UDim2.new(1, 0, 0, 21)
inputTextBox.Parent = inputOuter

inputTextBox.FocusLost:Connect(function()
    task.spawn(function()
        pcall(Info.Callback, inputTextBox.Text)
        if Info.Flag ~= nil then
            library.Flags[Info.Flag] = inputTextBox.Text
        end
    end)
end)
end

function sectiontable:Toggle(Info)
Info.Text = Info.Text or "Toggle"
Info.Flag = Info.Flag or nil
Info.Default = Info.Default or false
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""

if Info.Flag ~= nil then
    library.Flags[Info.Flag] = false
end

local insidetoggle = {}

local Toggled = false

local toggle = Instance.new("Frame")
toggle.Name = "Toggle"
toggle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
toggle.BackgroundTransparency = 1
toggle.Size = UDim2.new(1, 0, 0, 27)
toggle.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(toggle, Info.Tooltip)
end

local toggleText = Instance.new("TextLabel")
toggleText.Name = "ToggleText"
toggleText.Font = Enum.Font.GothamBold
toggleText.Text = Info.Text
toggleText.TextColor3 = Color3.fromRGB(217, 217, 217)
toggleText.TextSize = 11
toggleText.TextXAlignment = Enum.TextXAlignment.Left
toggleText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
toggleText.BackgroundTransparency = 1
toggleText.Position = UDim2.new(0.0488, 0, 0, 0)
toggleText.Size = UDim2.new(1, 0, 0, 27)
toggleText.Parent = toggle

local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Font = Enum.Font.SourceSans
toggleButton.Text = ""
toggleButton.TextColor3 = Color3.fromRGB(0, 0, 0)
toggleButton.TextSize = 14
toggleButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.BackgroundTransparency = 1
toggleButton.Size = UDim2.new(1, 0, 0, 27)
toggleButton.Parent = toggle

local toggleFrame = Instance.new("Frame")
toggleFrame.Name = "ToggleFrame"
toggleFrame.BackgroundColor3 = Color3.fromRGB(68, 68, 68)
toggleFrame.BorderSizePixel = 0
toggleFrame.Position = UDim2.new(0.783, 0, 0.222, 0)
toggleFrame.Size = UDim2.new(0, 30, 0, 15)
toggleFrame.Parent = toggle
Instance.new("UICorner", toggleFrame).CornerRadius = UDim.new(1, 0)

ColorElements[toggleFrame] = {Type = "Toggle", Enabled = false}

local circleIcon = Instance.new("Frame")
circleIcon.Name = "CheckIcon"
circleIcon.BackgroundColor3 = Color3.fromRGB(217, 217, 217)
circleIcon.BorderSizePixel = 0
circleIcon.Position = UDim2.new(0, 1, 0.067, 0)
circleIcon.Size = UDim2.new(0, 13, 0, 13)
circleIcon.Parent = toggleFrame
Instance.new("UICorner", circleIcon).CornerRadius = UDim.new(1, 0)

function insidetoggle:Set(bool)
    Toggled = bool
    if Info.Flag ~= nil then
        library.Flags[Info.Flag] = Toggled
    end
    ColorElements[toggleFrame].Enabled = Toggled
    
    TweenService:Create(circleIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Position = Toggled and UDim2.new(0, 16,0.067, 0) or UDim2.new(0, 1,0.067, 0)}):Play()
    if not Toggled then
        TweenService:Create(toggleFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(68, 68, 68)}):Play()
    elseif Toggled and not EditOpened then
        TweenService:Create(toggleFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(48, 207, 106)}):Play()
    end
    pcall(Info.Callback, Toggled)
end

if Info.Default then
    task.spawn(function()
        insidetoggle:Set(true)
    end)
end

toggleButton.MouseButton1Click:Connect(function()
    Toggled = not Toggled
    insidetoggle:Set(Toggled)
end)

if Info.Flag ~= nil then
    library._flagSetters[Info.Flag] = function(v) insidetoggle:Set(v) end
end

return insidetoggle
end

function sectiontable:Slider(Info)
Info.Text = Info.Text or "Slider"
Info.Default = Info.Default or 50
Info.Minimum = Info.Minimum or 1
Info.Flag = Info.Flag or nil
Info.Maximum = Info.Maximum or 100
Info.Postfix = Info.Postfix or ""
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""

if Info.Minimum > Info.Maximum then
local ValueBefore = Info.Minimum
Info.Minimum, Info.Maximum = Info.Maximum, ValueBefore
end

Info.Default = math.clamp(Info.Default, Info.Minimum, Info.Maximum)
local DefaultScale = (Info.Default - Info.Minimum) / (Info.Maximum - Info.Minimum)

local slider = Instance.new("Frame")
slider.Name = "Slider"
slider.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
slider.BackgroundTransparency = 1
slider.Position = UDim2.new(0, 0, 0.825, 0)
slider.Size = UDim2.new(1, 0, 0, 34)
slider.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(slider, Info.Tooltip)
end

local sliderText = Instance.new("TextLabel")
sliderText.Name = "SliderText"
sliderText.Font = Enum.Font.GothamBold
sliderText.Text = Info.Text
sliderText.TextColor3 = Color3.fromRGB(217, 217, 217)
sliderText.TextSize = 11
sliderText.TextXAlignment = Enum.TextXAlignment.Left
sliderText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sliderText.BackgroundTransparency = 1
sliderText.Position = UDim2.new(0.05, 0, 0, 0)
sliderText.Size = UDim2.new(1, 0, 0, 27)
sliderText.Parent = slider

local outerSlider = Instance.new("Frame")
outerSlider.Name = "OuterSlider"
outerSlider.BackgroundColor3 = Color3.fromRGB(68, 68, 68)
outerSlider.BorderSizePixel = 0
outerSlider.Position = UDim2.new(0.05, 0, 0.75, 0)
outerSlider.Size = UDim2.new(0.9, 0, 0, 4)
outerSlider.Parent = slider

local sliderCorner = Instance.new("UICorner")
sliderCorner.Name = "SliderCorner"
sliderCorner.CornerRadius = UDim.new(0, 100)
sliderCorner.Parent = outerSlider

local innerSlider = Instance.new("Frame")
innerSlider.Name = "InnerSlider"
innerSlider.BackgroundColor3 = Color3.fromRGB(48, 207, 106)
innerSlider.BorderSizePixel = 0
innerSlider.Size = UDim2.new(DefaultScale, 0, 0, 4)
innerSlider.ZIndex = 2
innerSlider.Parent = outerSlider

ColorElements[innerSlider] = {Type = "Slider", Enabled = false}

local innerSliderCorner = Instance.new("UICorner")
innerSliderCorner.Name = "InnerSliderCorner"
innerSliderCorner.CornerRadius = UDim.new(0, 100)
innerSliderCorner.Parent = innerSlider

local sliderValueBox = Instance.new("TextBox")
sliderValueBox.Name = "SliderValueText"
sliderValueBox.Font = Enum.Font.GothamBold
sliderValueBox.Text = tostring(Info.Default)..Info.Postfix
sliderValueBox.PlaceholderText = ""
sliderValueBox.TextColor3 = Color3.fromRGB(217, 217, 217)
sliderValueBox.PlaceholderColor3 = Color3.fromRGB(120, 120, 120)
sliderValueBox.TextSize = 11
sliderValueBox.TextXAlignment = Enum.TextXAlignment.Center
sliderValueBox.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
sliderValueBox.BorderSizePixel = 0
sliderValueBox.Position = UDim2.new(0.75, 0, 0.15, 0)
sliderValueBox.Size = UDim2.new(0.2, 0, 0, 19)
sliderValueBox.ClearTextOnFocus = false
sliderValueBox.Parent = slider

local sliderValueCorner = Instance.new("UICorner")
sliderValueCorner.Name = "SliderValueCorner"
sliderValueCorner.CornerRadius = UDim.new(0, 4)
sliderValueCorner.Parent = sliderValueBox

local sliderButton = Instance.new("TextButton")
sliderButton.Name = "SliderButton"
sliderButton.Font = Enum.Font.SourceSans
sliderButton.Text = ""
sliderButton.TextColor3 = Color3.fromRGB(0, 0, 0)
sliderButton.TextSize = 14
sliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sliderButton.BackgroundTransparency = 1
sliderButton.Position = UDim2.new(0.05, 0, 0.75, 0)
sliderButton.Size = UDim2.new(0.9, 0, 0, 4)
sliderButton.Parent = slider

task.spawn(function()
    pcall(Info.Callback, Info.Default)
    if Info.Flag ~= nil then
        library.Flags[Info.Flag] = Info.Default
    end
end)

local MinSize = 0
local MaxSize = 1

local SizeFromScale = (MinSize +  (MaxSize - MinSize)) * DefaultScale
SizeFromScale = SizeFromScale - (SizeFromScale % 2)

sliderButton.MouseButton1Down:Connect(function() -- Skidded from material ui hehe, sorry
    local MouseMove, MouseKill
    MouseMove = Mouse.Move:Connect(function()
        local Px = library:GetXY(outerSlider)
        local SizeFromScale = (MinSize +  (MaxSize - MinSize)) * Px
        local Value = math.floor(Info.Minimum + ((Info.Maximum - Info.Minimum) * Px))
        SizeFromScale = SizeFromScale - (SizeFromScale % 2)
        TweenService:Create(innerSlider, TweenInfo.new(0.1), {Size = UDim2.new(Px,0,0,4)}):Play()
        if Info.Flag ~= nil then
            library.Flags[Info.Flag] = Value
        end
        sliderValueBox.Text = tostring(Value)..Info.Postfix
        task.spawn(function()
            pcall(Info.Callback, Value)
        end)
    end)
    MouseKill = UserInputService.InputEnded:Connect(function(UserInput)
        if UserInput.UserInputType == Enum.UserInputType.MouseButton1 then
            MouseMove:Disconnect()
            MouseKill:Disconnect()
        end
    end)
end)

sliderValueBox.FocusLost:Connect(function(enterPressed)
    local raw = sliderValueBox.Text:gsub("[^%d%-]", "")
    local num = tonumber(raw)
    if num then
        num = math.clamp(num, Info.Minimum, Info.Maximum)
        local Px = (num - Info.Minimum) / (Info.Maximum - Info.Minimum)
        TweenService:Create(innerSlider, TweenInfo.new(0.15), {Size = UDim2.new(Px, 0, 0, 4)}):Play()
        if Info.Flag ~= nil then
            library.Flags[Info.Flag] = num
        end
        sliderValueBox.Text = tostring(num)..Info.Postfix
        task.spawn(function()
            pcall(Info.Callback, num)
        end)
    else
        sliderValueBox.Text = tostring(math.floor(Info.Minimum + ((Info.Maximum - Info.Minimum) * DefaultScale)))..Info.Postfix
    end
end)

if Info.Flag ~= nil then
    library._flagSetters[Info.Flag] = function(v)
        local val = math.clamp(v, Info.Minimum, Info.Maximum)
        library.Flags[Info.Flag] = val
        local Px = (val - Info.Minimum) / (Info.Maximum - Info.Minimum)
        sliderValueBox.Text = tostring(val)..Info.Postfix
        TweenService:Create(innerSlider, TweenInfo.new(0.15), {Size = UDim2.new(Px, 0, 0, 4)}):Play()
        pcall(function() Info.Callback(val) end)
    end
end

end

function sectiontable:Dropdown(Info)
Info.Text = Info.Text or "Dropdown"
Info.List = Info.List or {}
Info.Flag = Info.Flag or nil
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""
Info.Default = Info.Default or nil

local DropdownYSize = 27

if Info.Default ~= nil then
    task.spawn(function()
        pcall(Info.Callback, Info.Default)
    end)
    if Info.Flag ~= nil then
        library.Flags[Info.Flag] = Info.Default
    end
end

local insidedropdown = {}

local dropdown = Instance.new("Frame")
dropdown.Name = "Dropdown"
dropdown.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdown.BackgroundTransparency = 1
dropdown.Position = UDim2.new(0, 0, 0.638, 0)
dropdown.Size = UDim2.new(1, 0, 0, 27)
dropdown.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(dropdown, Info.Tooltip)
end

local dropdownText = Instance.new("TextLabel")
dropdownText.Name = "DropdownText"
dropdownText.Font = Enum.Font.GothamBold
dropdownText.Text = Info.Text
dropdownText.TextColor3 = Color3.fromRGB(217, 217, 217)
dropdownText.TextSize = 11
dropdownText.TextXAlignment = Enum.TextXAlignment.Left
dropdownText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownText.BackgroundTransparency = 1
dropdownText.Position = UDim2.new(0.0488, 0, 0, 0)
dropdownText.Size = UDim2.new(1, 0, 0, 27)
dropdownText.Parent = dropdown

local dropdownIcon = Instance.new("TextLabel")
dropdownIcon.Name = "DropdownIcon"
dropdownIcon.Text = ">"
dropdownIcon.TextColor3 = Color3.fromRGB(191, 191, 191)
dropdownIcon.Font = Enum.Font.GothamBold
dropdownIcon.TextSize = 14
dropdownIcon.AnchorPoint = Vector2.new(0.5, 0.5)
dropdownIcon.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownIcon.BackgroundTransparency = 1
dropdownIcon.Rotation = 180
dropdownIcon.Position = UDim2.new(1, -13, 0, 13)
dropdownIcon.Size = UDim2.new(0, 13, 0, 13)
dropdownIcon.ZIndex = 2
dropdownIcon.Parent = dropdown

local dropdownButton = Instance.new("TextButton")
dropdownButton.Name = "DropdownButton"
dropdownButton.Font = Enum.Font.SourceSans
dropdownButton.Text = ""
dropdownButton.TextColor3 = Color3.fromRGB(0, 0, 0)
dropdownButton.TextSize = 14
dropdownButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownButton.BackgroundTransparency = 1
dropdownButton.Size = UDim2.new(1, 0, 0, 27)
dropdownButton.Parent = dropdown

local dropdownContainer = Instance.new("Frame")
dropdownContainer.Name = "DropdownContainer"
dropdownContainer.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownContainer.BackgroundTransparency = 1
dropdownContainer.BorderSizePixel = 0
dropdownContainer.Size = UDim2.new(1, 0, 0, 27)
dropdownContainer.ClipsDescendants = true
dropdownContainer.Parent = dropdown
dropdownContainer.Visible = true

local dropdownContainerCorner = Instance.new("UICorner")
dropdownContainerCorner.CornerRadius = UDim.new(0, 3)
dropdownContainerCorner.Parent = dropdownContainer

local dropdownuIListLayout = Instance.new("UIListLayout")
dropdownuIListLayout.Name = "UIListLayout"
dropdownuIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
dropdownuIListLayout.Parent = dropdownContainer

local dropdownuIPadding = Instance.new("UIPadding")
dropdownuIPadding.Name = "UIPadding"
dropdownuIPadding.PaddingTop = UDim.new(0, 27)
dropdownuIPadding.Parent = dropdownContainer

local DropdownOpened = false
local savedSectionSize = nil
local savedSectionFrameSize = nil

if not library._dropdownTracker then
    library._dropdownTracker = {}
end
local myIndex = #library._dropdownTracker + 1
library._dropdownTracker[myIndex] = {
    dropdown = dropdown,
    dropdownContainer = dropdownContainer,
    dropdownIcon = dropdownIcon,
    sectionFrame = sectionFrame,
    section = section,
    isOpened = function() return DropdownOpened end,
    close = function()
        if not DropdownOpened then return end
        DropdownOpened = false
        local frameTarget = savedSectionFrameSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 27)
        local secTarget = savedSectionSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 33)
        savedSectionSize = nil
        savedSectionFrameSize = nil
        TweenService:Create(dropdownIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191), Rotation = 180}):Play()
        TweenService:Create(dropdown, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
        TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
        TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = 1}):Play()
        TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, frameTarget)}):Play()
        TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, secTarget)}):Play()
    end
}

function insidedropdown:Add(text)
DropdownYSize = DropdownYSize + 27

local dropdownContainerButton = Instance.new("Frame")
dropdownContainerButton.Name = "DropdownContainerButton"
dropdownContainerButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownContainerButton.BackgroundTransparency = 1
dropdownContainerButton.Size = UDim2.new(1, 0, 0, 27)
dropdownContainerButton.Parent = dropdownContainer

local dropdownbuttonText = Instance.new("TextLabel")
dropdownbuttonText.Name = "ButtonText"
dropdownbuttonText.Font = Enum.Font.GothamBold
dropdownbuttonText.Text = text
dropdownbuttonText.TextColor3 = Color3.fromRGB(191, 191, 191)
dropdownbuttonText.TextSize = 11
dropdownbuttonText.TextXAlignment = Enum.TextXAlignment.Left
dropdownbuttonText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownbuttonText.BackgroundTransparency = 1
dropdownbuttonText.Position = UDim2.new(0.0488, 0, 0, 0)
dropdownbuttonText.Size = UDim2.new(1, 0, 0, 28)
dropdownbuttonText.Parent = dropdownContainerButton

local dropdownContainerTextButton = Instance.new("TextButton")
dropdownContainerTextButton.Name = "DropdownContainerButton"
dropdownContainerTextButton.Font = Enum.Font.SourceSans
dropdownContainerTextButton.Text = ""
dropdownContainerTextButton.TextColor3 = Color3.fromRGB(0, 0, 0)
dropdownContainerTextButton.TextSize = 14
dropdownContainerTextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dropdownContainerTextButton.BackgroundTransparency = 1
dropdownContainerTextButton.Size = UDim2.new(1, 0, 0, 27)
dropdownContainerTextButton.Parent = dropdownContainerButton

dropdownContainerTextButton.MouseEnter:Connect(function()
    TweenService:Create(dropdownbuttonText, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(255, 255, 255)}):Play()
end)

dropdownContainerTextButton.MouseLeave:Connect(function()
    TweenService:Create(dropdownbuttonText, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191)}):Play()
end)

dropdownContainerTextButton.MouseButton1Click:Connect(function()
    DropdownOpened = false
    
    task.spawn(function()
        pcall(Info.Callback, dropdownbuttonText.Text)
    end)
    if Info.Flag ~= nil then
        library.Flags[Info.Flag] = dropdownbuttonText.Text
    end
    dropdownText.Text = dropdownbuttonText.Text
    
    TweenService:Create(dropdownIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191), Rotation = 180}):Play()
    TweenService:Create(dropdown, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = 1}):Play()
    TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, savedSectionFrameSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 27))}):Play()
    TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, (savedSectionSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 33)))}):Play()
    savedSectionSize = nil
    savedSectionFrameSize = nil
end)
end

function insidedropdown:Refresh(RefreshInfo)
RefreshInfo.Text = RefreshInfo.Text or dropdownText.Text
RefreshInfo.List = RefreshInfo.List or Info.List

if DropdownOpened then
    DropdownOpened = false
end

for _,v in pairs(dropdownContainer:GetChildren()) do
    if v.ClassName == "Frame" then
        v:Destroy()
    end
end

DropdownYSize = 27

for _,v in pairs(RefreshInfo.List) do
    insidedropdown:Add(v)
end

TweenService:Create(dropdownIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191), Rotation = 180}):Play()
TweenService:Create(dropdown, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = 1}):Play()
end

for _,v in pairs(Info.List) do
insidedropdown:Add(v)
end

Closed:GetPropertyChangedSignal("Value"):Connect(function()
    if not Closed.Value then
    DropdownOpened = false
    
    TweenService:Create(dropdownIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191), Rotation = 180}):Play()
    TweenService:Create(dropdown, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = 1}):Play()
    TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, savedSectionFrameSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 27))}):Play()
    TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, (savedSectionSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 33)))}):Play()
    savedSectionSize = nil
    savedSectionFrameSize = nil
    end
end)

dropdownButton.MouseButton1Click:Connect(function()
    DropdownOpened = not DropdownOpened
    
    if DropdownOpened then
        savedSectionSize = section.Size.Y.Offset
        savedSectionFrameSize = sectionFrame.Size.Y.Offset
    end
    
    TweenService:Create(dropdownIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = DropdownOpened and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(191, 191, 191), Rotation = DropdownOpened and 90 or 180}):Play()
    TweenService:Create(dropdown, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = DropdownOpened and UDim2.new(1, 0, 0, DropdownYSize) or UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = DropdownOpened and UDim2.new(1, 0, 0, DropdownYSize) or UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(dropdownContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = DropdownOpened and .96 or 1}):Play()
    if DropdownOpened then
        TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset + DropdownYSize - 27)}):Play()
        TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset + DropdownYSize - 27 + 6)}):Play()
    else
        TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, savedSectionFrameSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 27))}):Play()
        TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = UDim2.new(1, 0, 0, (savedSectionSize or (sectionFrame.Size.Y.Offset - DropdownYSize + 33)))}):Play()
        savedSectionSize = nil
        savedSectionFrameSize = nil
    end
end)

if Info.Flag ~= nil then
    library._flagSetters[Info.Flag] = function(v)
        library.Flags[Info.Flag] = v
        dropdownbuttonText.Text = tostring(v)
        dropdownText.Text = tostring(v)
        pcall(function() Info.Callback(v) end)
    end
end

return insidedropdown
end

function sectiontable:RadioButton(Info)
Info.Text = Info.Text or "Radio Button"
Info.Options = Info.Options or {}
Info.Flag = Info.Flag or nil
Info.Callback = Info.Callback or function() end
Info.Tooltip = Info.Tooltip or ""
Info.Default = Info.Default or nil

local RadioOpened = false

RadioYSize = 27

if Info.Default ~= nil then
    task.spawn(function()
        pcall(Info.Callback, Info.Default)
    end)
    if Info.Flag ~= nil then
        library.Flags[Info.Flag] = Info.Default
    end
end

local insideradio = {}

local radioButton = Instance.new("Frame")
radioButton.Name = "RadioButton"
radioButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioButton.BackgroundTransparency = 1
radioButton.Size = UDim2.new(1, 0, 0, 27)
radioButton.Parent = sectionFrame

if Info.Tooltip ~= "" then
    AddTooltip(radioButton, Info.Tooltip)
end

local button = Instance.new("Frame")
button.Name = "Button"
button.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
button.BackgroundTransparency = 1
button.Size = UDim2.new(1, 0, 0, 27)
button.Parent = radioButton

local radioButtonTextButton = Instance.new("TextButton")
radioButtonTextButton.Name = "RadioButtonTextButton"
radioButtonTextButton.Font = Enum.Font.SourceSans
radioButtonTextButton.Text = ""
radioButtonTextButton.TextColor3 = Color3.fromRGB(0, 0, 0)
radioButtonTextButton.TextSize = 14
radioButtonTextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioButtonTextButton.BackgroundTransparency = 1
radioButtonTextButton.Size = UDim2.new(1, 0, 0, 27)
radioButtonTextButton.Parent = button

local radioButtonText = Instance.new("TextLabel")
radioButtonText.Name = "RadioButtonText"
radioButtonText.Font = Enum.Font.GothamBold
radioButtonText.Text = Info.Text
radioButtonText.TextColor3 = Color3.fromRGB(217, 217, 217)
radioButtonText.TextSize = 11
radioButtonText.TextXAlignment = Enum.TextXAlignment.Left
radioButtonText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioButtonText.BackgroundTransparency = 1
radioButtonText.Position = UDim2.new(0.0488, 0, 0, 0)
radioButtonText.Size = UDim2.new(1, 0, 0, 27)
radioButtonText.Parent = button

local radioButtonIcon = Instance.new("TextLabel")
radioButtonIcon.Name = "RadioButtonIcon"
radioButtonIcon.Text = "∨"
radioButtonIcon.TextColor3 = Color3.fromRGB(191, 191, 191)
radioButtonIcon.Font = Enum.Font.GothamBold
radioButtonIcon.TextSize = 14
radioButtonIcon.AnchorPoint = Vector2.new(1, 0)
radioButtonIcon.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioButtonIcon.BackgroundTransparency = 1
radioButtonIcon.BorderSizePixel = 0
radioButtonIcon.Position = UDim2.new(0, 155, 0, 7)
radioButtonIcon.Size = UDim2.new(0, 13, 0, 13)
radioButtonIcon.Parent = button

local radioButtonIcon2 = Instance.new("Frame")
radioButtonIcon2.Name = "RadioButtonIcon2"
radioButtonIcon2.BackgroundColor3 = Color3.fromRGB(191, 191, 191)
radioButtonIcon2.BorderSizePixel = 0
radioButtonIcon2.Position = UDim2.new(0, 138, 0, 7)
radioButtonIcon2.Size = UDim2.new(0, 13, 0, 13)
radioButtonIcon2.Parent = button
Instance.new("UICorner", radioButtonIcon2).CornerRadius = UDim.new(1, 0)
radioButtonIcon2.Position = UDim2.new(0, 138, 0, 7)
radioButtonIcon2.Size = UDim2.new(0, 13, 0, 13)
radioButtonIcon2.Parent = button

local radioContainer = Instance.new("Frame")
radioContainer.Name = "RadioContainer"
radioContainer.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioContainer.BackgroundTransparency = 1
radioContainer.Size = UDim2.new(1, 0, 0, 27)
radioContainer.Parent = radioButton
radioContainer.ClipsDescendants = true

local radioUILayout = Instance.new("UIListLayout")
radioUILayout.Name = "RadioUILayout"
radioUILayout.SortOrder = Enum.SortOrder.LayoutOrder
radioUILayout.Parent = radioContainer

local radiouIPadding = Instance.new("UIPadding")
radiouIPadding.Name = "UIPadding"
radiouIPadding.PaddingTop = UDim.new(0, 27)
radiouIPadding.Parent = radioContainer

local RadioSelected = nil

function insideradio:Button(text)
RadioYSize = RadioYSize + 27

local radio = Instance.new("Frame")
radio.Name = "Radio"
radio.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radio.BackgroundTransparency = 1
radio.Size = UDim2.new(1, 0, 0, 27)
radio.Parent = radioContainer

local radioTextButton = Instance.new("TextButton")
radioTextButton.Name = "RadioTextButton"
radioTextButton.Font = Enum.Font.SourceSans
radioTextButton.Text = ""
radioTextButton.TextColor3 = Color3.fromRGB(0, 0, 0)
radioTextButton.TextSize = 14
radioTextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioTextButton.BackgroundTransparency = 1
radioTextButton.Size = UDim2.new(1, 0, 0, 27)
radioTextButton.Parent = radio

local radioOuter = Instance.new("Frame")
radioOuter.Name = "RadioOuter"
radioOuter.BackgroundColor3 = Color3.fromRGB(191, 191, 191)
radioOuter.BackgroundTransparency = 0
radioOuter.BorderSizePixel = 0
radioOuter.Position = UDim2.new(0.865, 0, 0.185, 0)
radioOuter.Size = UDim2.new(0, 17, 0, 17)
radioOuter.Parent = radio
Instance.new("UICorner", radioOuter).CornerRadius = UDim.new(1, 0)

local radioInner = Instance.new("Frame")
radioInner.Name = "RadioInner"
radioInner.AnchorPoint = Vector2.new(0.5, 0.5)
radioInner.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioInner.BackgroundTransparency = 0
radioInner.BorderSizePixel = 0
radioInner.Position = UDim2.new(0.5, 0, 0.5, 0)
radioInner.Size = UDim2.new(0, 7, 0, 7)
radioInner.Parent = radioOuter
Instance.new("UICorner", radioInner).CornerRadius = UDim.new(1, 0)

ColorElements[radioInner] = {Type = "Toggle", Enabled = false}
ColorElements[radioOuter] = {Type = "Toggle", Enabled = false}

local radioText = Instance.new("TextLabel")
radioText.Name = "RadioText"
radioText.Font = Enum.Font.GothamBold
radioText.Text = text
radioText.TextColor3 = Color3.fromRGB(191, 191, 191)
radioText.TextSize = 11
radioText.TextXAlignment = Enum.TextXAlignment.Left
radioText.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
radioText.BackgroundTransparency = 1
radioText.Position = UDim2.new(0.0488, 0, 0, 0)
radioText.Size = UDim2.new(1, 0, 0, 27)
radioText.Parent = radio

radio.MouseEnter:Connect(function()
    if RadioOpened and RadioSelected ~= radio or RadioSelected == nil then
    TweenService:Create(radioText, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(217, 217, 217)}):Play()
    TweenService:Create(radioInner, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(217, 217, 217)}):Play()
    TweenService:Create(radioOuter, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(217, 217, 217)}):Play()
    end
end)

radio.MouseLeave:Connect(function()
    if RadioOpened and RadioSelected ~= radio or RadioSelected == nil then
    TweenService:Create(radioText, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191)}):Play()
    TweenService:Create(radioInner, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(191, 191, 191)}):Play()
    TweenService:Create(radioOuter, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(191, 191, 191)}):Play()
    end
end)

radioTextButton.MouseButton1Click:Connect(function()
    task.spawn(function()
        pcall(Info.Callback, radioText.Text)
    end)
    if Info.Flag ~= nil then
        library.Flags[Info.Flag] = radioText.Text
    end
    
    ColorElements[radioInner].Enabled = true
    ColorElements[radioOuter].Enabled = true
    
    RadioSelected = radio
    
    for _,v in pairs(radioContainer:GetChildren()) do
        if v.ClassName == "Frame" and v ~= radio then
            ColorElements[v.RadioOuter].Enabled = false
            ColorElements[v.RadioOuter.RadioInner].Enabled = false
            TweenService:Create(v.RadioOuter, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(191, 191, 191)}):Play()
            TweenService:Create(v.RadioOuter.RadioInner, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = Color3.fromRGB(191, 191, 191)}):Play()
            TweenService:Create(v.RadioText, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(191, 191, 191)}):Play()
        end
    end
    
    TweenService:Create(radioText, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = Color3.fromRGB(255, 255, 255)}):Play()
    
    if not EditOpened then
        TweenService:Create(radioInner, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = RadioOpened and Color3.fromRGB(48, 207, 106) or Color3.fromRGB(191, 191, 191)}):Play()
        TweenService:Create(radioOuter, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = RadioOpened and Color3.fromRGB(48, 207, 106) or Color3.fromRGB(191, 191, 191)}):Play()
    end
end)

end

radioButtonTextButton.MouseButton1Click:Connect(function()
    RadioOpened = not RadioOpened
    
    TweenService:Create(radioButtonIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = RadioOpened and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(191, 191, 191)}):Play()
    TweenService:Create(radioButtonIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Rotation = RadioOpened and -180 or -90}):Play()
    TweenService:Create(radioButtonIcon2, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = RadioOpened and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(191, 191, 191)}):Play()
    TweenService:Create(radioButton, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, RadioYSize) or UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(radioContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, RadioYSize) or UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(radioContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = RadioOpened and .96 or 1}):Play()
    TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset + RadioYSize - 27) or UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset - RadioYSize + 27)}):Play()
    TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset + RadioYSize - 27 + 6) or UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset - RadioYSize + 33)}):Play()
end)

for _,v in pairs(Info.Options) do
    insideradio:Button(v)
end

Closed:GetPropertyChangedSignal("Value"):Connect(function()
    if not Closed.Value then
    RadioOpened = false
    
    TweenService:Create(radioButtonIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {TextColor3 = RadioOpened and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(191, 191, 191)}):Play()
    TweenService:Create(radioButtonIcon, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Rotation = RadioOpened and -180 or -90}):Play()
    TweenService:Create(radioButtonIcon2, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundColor3 = RadioOpened and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(191, 191, 191)}):Play()
    TweenService:Create(radioButton, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, RadioYSize) or UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(radioContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, RadioYSize) or UDim2.new(1, 0, 0, 27)}):Play()
    TweenService:Create(radioContainer, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = RadioOpened and .96 or 1}):Play()
    TweenService:Create(sectionFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset + RadioYSize - 27) or UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset - RadioYSize + 27)}):Play()
    TweenService:Create(section, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {Size = RadioOpened and UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset + RadioYSize - 27 + 6) or UDim2.new(1, 0, 0, sectionFrame.Size.Y.Offset - RadioYSize + 33)}):Play()
    end
end)

return insideradio
end

return sectiontable
end

tabTextButton.MouseButton1Click:Connect(function()
    TabSelected = tabFrame
    task.spawn(function()
    for _,v in pairs(main:GetChildren()) do
        if v.Name == "LeftContainer" or v.Name == "RightContainer" then
            v.Visible = false
        end
    end
    end)
    for _,v in pairs(scrollingContainer:GetChildren()) do
        if v ~= tabButton and v.Name == "TabButton" then
            TweenService:Create(v.TabFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = .96}):Play()
        end
    end
    TweenService:Create(tabFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = .85}):Play()
    leftContainer.Visible = true
    rightContainer.Visible = true
end)

function tab:Select()
    TabSelected = tabFrame
    task.spawn(function()
    for _,v in pairs(main:GetChildren()) do
        if v.Name == "LeftContainer" or v.Name == "RightContainer" then
            v.Visible = false
        end
    end
    end)
    for _,v in pairs(scrollingContainer:GetChildren()) do
        if v ~= tabButton and v.Name == "TabButton" then
            TweenService:Create(v.TabFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = .96}):Play()
        end
    end
    TweenService:Create(tabFrame, TweenInfo.new(.15, Enum.EasingStyle.Linear, Enum.EasingDirection.In), {BackgroundTransparency = .85}):Play()
    leftContainer.Visible = true
    rightContainer.Visible = true
end

return tab
end

local uIListLayout = Instance.new("UIListLayout")
uIListLayout.Name = "UIListLayout"
uIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
uIListLayout.Parent = scrollingContainer

local uIPadding = Instance.new("UIPadding")
uIPadding.Name = "UIPadding"
uIPadding.Parent = scrollingContainer

local frame2 = Instance.new("Frame")
frame2.Name = "Frame"
frame2.AnchorPoint = Vector2.new(1, 0.5)
frame2.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
frame2.BorderSizePixel = 0
frame2.Position = UDim2.new(1, 0, 0.501, 0)
frame2.Size = UDim2.new(0, 1, 0, 284)
frame2.Parent = tabContainer

local uIStroke2 = Instance.new("UIStroke")
uIStroke2.Name = "UIStroke"
uIStroke2.Color = Color3.fromRGB(61, 61, 61)
uIStroke2.Parent = main

return window
end

game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.UserInputType == Enum.UserInputType.MouseButton1 then
        local mouse = game.Players.LocalPlayer:GetMouse()
        local target = mouse.Target
        local skip = false
        if target then
            local obj = target
            while obj and obj ~= game.CoreGui do
                if obj:IsA("Frame") and obj.Name == "Dropdown" then
                    skip = true
                    break
                end
                obj = obj.Parent
            end
        end
        if not skip and library._dropdownTracker then
            for _, info in pairs(library._dropdownTracker) do
                if info.isOpened() then
                    info.close()
                end
            end
        end
    end
end)

return library
