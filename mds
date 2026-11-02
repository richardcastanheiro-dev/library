
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local Lib = {}
Lib.Version = "6.4.0"
Lib.Windows = {}
Lib.Controls = {}
Lib._watermarks = {}

local Theme = {
	Bg = Color3.fromRGB(12, 12, 16),
	Surface = Color3.fromRGB(20, 20, 26),
	SurfaceAlt = Color3.fromRGB(30, 30, 38),
	SurfaceHi = Color3.fromRGB(42, 42, 52),
	Stroke = Color3.fromRGB(55, 55, 70),
	Accent = Color3.fromRGB(139, 108, 255),
	AccentSoft = Color3.fromRGB(180, 160, 255),
	Text = Color3.fromRGB(245, 245, 252),
	TextDim = Color3.fromRGB(145, 145, 165),
	Success = Color3.fromRGB(94, 226, 158),
	Danger = Color3.fromRGB(244, 92, 116)
}

local Fast = TweenInfo.new(0.12, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
local Normal = TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
local Slow = TweenInfo.new(0.32, Enum.EasingStyle.Quint, Enum.EasingDirection.Out)
local In = TweenInfo.new(0.18, Enum.EasingStyle.Quart, Enum.EasingDirection.In)

Lib.Theme = Theme
Lib.Tween = {
	Fast = Fast,
	Normal = Normal,
	Slow = Slow,
	In = In
}

local IconLoaded, IconModule = pcall(function()
	return loadstring(game:HttpGet(
		"https://raw.githubusercontent.com/deividcomsono/lucide-roblox-direct/refs/heads/main/source.lua"
	))()
end)

Lib.Icons = IconLoaded and IconModule or nil

local function make(class, properties, parent)
	local object = Instance.new(class)
	if properties then
		for key, value in pairs(properties) do
			object[key] = value
		end
	end
	if parent then
		object.Parent = parent
	end
	return object
end

local function corner(parent, radius)
	local item = Instance.new("UICorner")
	item.CornerRadius = UDim.new(0, radius)
	item.Parent = parent
	return item
end

local function stroke(parent, color, transparency)
	local item = Instance.new("UIStroke")
	item.Color = color or Theme.Stroke
	item.Transparency = transparency or 0
	item.Thickness = 1
	item.Parent = parent
	return item
end

local function getViewport()
	local camera = workspace.CurrentCamera
	if camera then
		return camera.ViewportSize
	end
	return Vector2.new(1280, 720)
end

local function safeCall(callback, ...)
	if type(callback) ~= "function" then
		return
	end
	local ok, err = pcall(callback, ...)
	if not ok then
		warn("[MikuLib] " .. tostring(err))
	end
end

local function getShared(create)
	local environment
	local ok, value = pcall(function()
		if getgenv then
			return getgenv()
		end
		return _G
	end)
	if ok and type(value) == "table" then
		environment = value
	else
		environment = _G
	end
	if type(environment) ~= "table" then
		return nil
	end
	local sharedTable = environment.Shared
	if type(sharedTable) ~= "table" and create then
		sharedTable = {}
		environment.Shared = sharedTable
	end
	if type(sharedTable) == "table" then
		return sharedTable
	end
	return nil
end

local function getPageFrom(object)
	local current = object
	while current do
		if current:IsA("ScrollingFrame") and current:GetAttribute("MikuPage") then
			return current
		end
		current = current.Parent
	end
	return nil
end

local function updateCanvas(page)
	if not page or not page.Parent then
		return
	end
	local holder = page:FindFirstChild("Holder")
	local layout = holder and holder:FindFirstChildOfClass("UIListLayout")
	local contentHeight = layout and layout.AbsoluteContentSize.Y or 0
	local height = math.max(contentHeight + 32, page.AbsoluteSize.Y)
	page.CanvasSize = UDim2.fromOffset(0, height)
end

local function refreshPage(page)
	if not page then
		return
	end
	updateCanvas(page)
	if page:GetAttribute("MikuCanvasQueued") then
		return
	end
	page:SetAttribute("MikuCanvasQueued", true)
	task.defer(function()
		if page and page.Parent then
			page:SetAttribute("MikuCanvasQueued", false)
			updateCanvas(page)
		end
	end)
end

local function resolveHost(parent)
	if not parent then
		return nil
	end
	if parent:IsA("ScrollingFrame") then
		return parent:FindFirstChild("Holder") or parent
	end
	return parent
end

local function nextLayoutOrder(host)
	local order = (host:GetAttribute("MikuLayoutOrder") or 0) + 1
	host:SetAttribute("MikuLayoutOrder", order)
	return order
end

local function prepareControl(parent, height, class)
	local host = resolveHost(parent)
	local page = getPageFrom(host)
	local object = make(class or "Frame", {
		Size = UDim2.new(1, 0, 0, height),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		LayoutOrder = nextLayoutOrder(host)
	}, host)
	return object, page, host
end

local function formatValue(value)
	if value == math.floor(value) then
		return tostring(math.floor(value))
	end
	return string.format("%.2f", value)
end

local function registerControl(instance, setters)
	Lib.Controls[instance] = setters
	return instance
end

function Lib:GetIcon(name)
	if not self.Icons or type(name) ~= "string" then
		return nil
	end
	local success, icon = pcall(function()
		return self.Icons.GetAsset(name)
	end)
	if success then
		return icon
	end
	return nil
end

function Lib:GetCustomIcon(value)
	if value == nil then
		return nil
	end
	if type(value) == "number" or tonumber(value) then
		value = "rbxassetid://" .. tostring(value)
	end
	value = tostring(value)
	if value:match("^rbxasset") or value:match("^https?://") then
		return {
			Url = value,
			ImageRectOffset = Vector2.zero,
			ImageRectSize = Vector2.zero,
			Custom = true
		}
	end
	return self:GetIcon(value)
end

function Lib:GetIconNames()
	if not self.Icons or type(self.Icons.Icons) ~= "table" then
		return {}
	end
	return self.Icons.Icons
end

function Lib.newDraggable(target, handle, isLocked)
	local dragging = false
	local startMouse
	local startX
	local startY
	local moveConnection
	local endConnection
	local handleConnection

	local function locked()
		if type(isLocked) == "function" then
			return isLocked()
		end
		return isLocked == true
	end

	local function stop()
		dragging = false
		if moveConnection then
			moveConnection:Disconnect()
			moveConnection = nil
		end
		if endConnection then
			endConnection:Disconnect()
			endConnection = nil
		end
	end

	handleConnection = handle.InputBegan:Connect(function(input)
		if locked() then
			return
		end
		if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then
			return
		end
		dragging = true
		startMouse = UserInputService:GetMouseLocation()
		startX = target.Position.X.Offset
		startY = target.Position.Y.Offset
		if moveConnection then
			moveConnection:Disconnect()
			moveConnection = nil
		end
		if endConnection then
			endConnection:Disconnect()
			endConnection = nil
		end
		moveConnection = UserInputService.InputChanged:Connect(function(changed)
			if not dragging then
				return
			end
			if changed.UserInputType ~= Enum.UserInputType.MouseMovement and changed.UserInputType ~= Enum.UserInputType.Touch then
				return
			end
			local mouse = UserInputService:GetMouseLocation()
			local viewport = getViewport()
			local size = target.AbsoluteSize
			local maxX = math.max(0, viewport.X - size.X)
			local maxY = math.max(0, viewport.Y - size.Y)
			local nextX = math.clamp(startX + mouse.X - startMouse.X, 0, maxX)
			local nextY = math.clamp(startY + mouse.Y - startMouse.Y, 0, maxY)
			target.Position = UDim2.fromOffset(nextX, nextY)
		end)
		endConnection = UserInputService.InputEnded:Connect(function(changed)
			if changed.UserInputType == Enum.UserInputType.MouseButton1 or changed.UserInputType == Enum.UserInputType.Touch then
				stop()
			end
		end)
	end)

	local api = {}
	function api.Destroy()
		if handleConnection then
			handleConnection:Disconnect()
			handleConnection = nil
		end
		stop()
	end
	return api
end

function Lib.newResize(target, callback)
	local dragging = false
	local startMouse
	local startSize
	local moveConnection
	local endConnection

	local handle = Instance.new("TextButton")
	handle.Name = "ResizeHandle"
	handle.Parent = target
	handle.AnchorPoint = Vector2.new(1, 1)
	handle.Position = UDim2.new(1, -4, 1, -4)
	handle.Size = UDim2.fromOffset(30, 30)
	handle.BackgroundColor3 = Theme.Bg
	handle.BackgroundTransparency = 0.1
	handle.BorderSizePixel = 0
	handle.Text = "⤡"
	handle.TextColor3 = Theme.TextDim
	handle.TextSize = 22
	handle.Font = Enum.Font.GothamBold
	handle.AutoButtonColor = false
	handle.Active = true
	handle.Selectable = false
	handle.ZIndex = 999

	corner(handle, 7)
	stroke(handle, Theme.Accent, 0.55)

	local function stop()
		dragging = false
		if moveConnection then
			moveConnection:Disconnect()
			moveConnection = nil
		end
		if endConnection then
			endConnection:Disconnect()
			endConnection = nil
		end
	end

	handle.MouseEnter:Connect(function()
		TweenService:Create(handle, Fast, {
			TextColor3 = Theme.Accent,
			BackgroundTransparency = 0
		}):Play()
	end)

	handle.MouseLeave:Connect(function()
		if not dragging then
			TweenService:Create(handle, Fast, {
				TextColor3 = Theme.TextDim,
				BackgroundTransparency = 0.1
			}):Play()
		end
	end)

	handle.InputBegan:Connect(function(input)
		if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then
			return
		end
		stop()
		dragging = true
		startMouse = UserInputService:GetMouseLocation()
		startSize = target.AbsoluteSize

		moveConnection = UserInputService.InputChanged:Connect(function(changed)
			if not dragging then
				return
			end
			if changed.UserInputType ~= Enum.UserInputType.MouseMovement and changed.UserInputType ~= Enum.UserInputType.Touch then
				return
			end
			local mouse = UserInputService:GetMouseLocation()
			local delta = mouse - startMouse
			local viewport = getViewport()

			local maxWidth = math.max(320, viewport.X - 40)
			local maxHeight = math.max(240, viewport.Y - 40)
			local minWidth = math.min(360, maxWidth)
			local minHeight = math.min(240, maxHeight)

			local width = math.clamp(startSize.X + delta.X, minWidth, maxWidth)
			local height = math.clamp(startSize.Y + delta.Y, minHeight, maxHeight)

			target.Size = UDim2.fromOffset(width, height)

			if callback then
				callback(width, height)
			end
		end)

		endConnection = UserInputService.InputEnded:Connect(function(changed)
			if changed.UserInputType == Enum.UserInputType.MouseButton1 or changed.UserInputType == Enum.UserInputType.Touch then
				stop()
			end
		end)
	end)

	return {
		Destroy = function()
			stop()
			handle:Destroy()
		end
	}
end

local Window = {}
Window.__index = Window

function Window:_cancelTweens()
	for _, tween in ipairs(self._tweens) do
		if tween then
			pcall(function()
				tween:Cancel()
			end)
		end
	end
	self._tweens = {}
end

function Window:ClampPosition()
	if not self._wrapper then
		return
	end
	local viewport = getViewport()
	local size = self._wrapper.AbsoluteSize
	if size.X < 1 or size.Y < 1 then
		size = Vector2.new(self._wrapper.Size.X.Offset, self._wrapper.Size.Y.Offset)
	end
	local maxX = math.max(0, viewport.X - size.X)
	local maxY = math.max(0, viewport.Y - size.Y)
	local x = math.clamp(self._wrapper.Position.X.Offset, 0, maxX)
	local y = math.clamp(self._wrapper.Position.Y.Offset, 0, maxY)
	self._wrapper.Position = UDim2.fromOffset(x, y)
end

function Window:Open()
	if self._destroyed or self._visible then
		return
	end
	self._visible = true
	self._wrapper.Visible = true
	self:_cancelTweens()
	self._animToken = self._animToken + 1
	self._rootScale.Scale = 0.94
	self._root.GroupTransparency = 1
	local scaleTween = TweenService:Create(self._rootScale, TweenInfo.new(0.34, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
		Scale = 1
	})
	local fadeTween = TweenService:Create(self._root, TweenInfo.new(0.22, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
		GroupTransparency = 0
	})
	self._tweens = { scaleTween, fadeTween }
	scaleTween:Play()
	fadeTween:Play()
	self:ClampPosition()
	for _, entry in ipairs(self._tabs) do
		if entry.Page.Visible then
			refreshPage(entry.Page)
		end
	end
end

function Window:Close()
	if self._destroyed then
		return
	end
	if not self._visible and not self._wrapper.Visible then
		return
	end
	self._visible = false
	self:_cancelTweens()
	self._animToken = self._animToken + 1
	local token = self._animToken
	local fadeTween = TweenService:Create(self._root, TweenInfo.new(0.16, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
		GroupTransparency = 1
	})
	local scaleTween = TweenService:Create(self._rootScale, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
		Scale = 0.92
	})
	self._tweens = { fadeTween, scaleTween }
	fadeTween:Play()
	scaleTween:Play()
	scaleTween.Completed:Connect(function()
		if self._destroyed then
			return
		end
		if self._animToken == token and not self._visible then
			self._wrapper.Visible = false
			self._rootScale.Scale = 1
			self._root.GroupTransparency = 1
		end
	end)
end

function Window:SetVisible(value)
	if value then
		self:Open()
	else
		self:Close()
	end
end

function Window:Toggle()
	if self._visible then
		self:Close()
	else
		self:Open()
	end
end

function Window:GetVisible()
	return self._visible and self._wrapper.Visible
end

function Window:Minimize()
	if self._destroyed then
		return
	end
	self._minimized = not self._minimized
	local target = self._minimized and UDim2.new(1, 0, 0, 55) or UDim2.fromScale(1, 1)
	TweenService:Create(self._root, Normal, {
		Size = target
	}):Play()
end

function Window:SetSize(width, height)
	if self._destroyed then
		return
	end
	local nextWidth
	local nextHeight
	if typeof(width) == "UDim2" then
		nextWidth = width.X.Offset
		nextHeight = width.Y.Offset
	elseif typeof(width) == "Vector2" then
		nextWidth = width.X
		nextHeight = width.Y
	elseif type(width) == "number" then
		nextWidth = width
		nextHeight = height or self._baseSize.Y
	else
		return
	end
	local viewport = getViewport()
	local maxWidth = math.max(320, viewport.X - 40)
	local minWidth = math.min(360, maxWidth)
	local maxHeight = math.max(240, viewport.Y - 40)
	local minHeight = math.min(240, maxHeight)
	nextWidth = math.clamp(nextWidth, minWidth, maxWidth)
	nextHeight = math.clamp(nextHeight, minHeight, maxHeight)
	self._baseSize = Vector2.new(nextWidth, nextHeight)
	self._wrapper.Size = UDim2.fromOffset(nextWidth, nextHeight)
	self:ClampPosition()
end

function Window:SetTitle(title, subtitle)
	if title ~= nil then
		self._titleLabel.Text = tostring(title)
	end
	if subtitle ~= nil then
		self._subtitleLabel.Text = tostring(subtitle)
	end
end

function Window:SetSubtitle(subtitle)
	if subtitle ~= nil then
		self._subtitleLabel.Text = tostring(subtitle)
	end
end

function Window:SetProfile(profile)
	if self._destroyed then
		return
	end
	profile = type(profile) == "table" and profile or {}
	if profile.Hidden ~= nil then
		self._profileCard.Visible = not profile.Hidden
	end
	if profile.Name ~= nil then
		self._profileName.Text = tostring(profile.Name)
	end
	if profile.Tag ~= nil then
		self._profileTag.Text = tostring(profile.Tag)
	end
	if profile.Image ~= nil then
		self._avatar.Image = tostring(profile.Image)
	end
end

function Window:SelectTab(target)
	local entry
	if type(target) == "table" and target.Button and target.Page then
		entry = target
	else
		for _, item in ipairs(self._tabs) do
			if item == target or item.Name == target or item.Button == target or item.Page == target or item.Button.Name == target or item.Page.Name == target then
				entry = item
				break
			end
		end
		if not entry and type(target) == "number" then
			entry = self._tabs[target]
		end
	end
	if not entry then
		return
	end
	for _, item in ipairs(self._tabs) do
		local active = item == entry
		item.Active = active
		item.Page.Visible = active
		item.Indicator.Visible = active
		item.Label.TextColor3 = active and Theme.Text or Theme.TextDim
		item.Label.Font = active and Enum.Font.GothamBold or Enum.Font.GothamMedium
		item.Button.BackgroundColor3 = active and Theme.Accent or Theme.SurfaceAlt
		item.Button.BackgroundTransparency = active and 0.82 or 0.55
		local itemStroke = item.Button:FindFirstChildOfClass("UIStroke")
		if itemStroke then
			itemStroke.Color = active and Theme.Accent or Theme.Stroke
			itemStroke.Transparency = active and 0.35 or 0.45
		end
		local tabIcon = item.Button:FindFirstChild("TabIcon")
		if tabIcon then
			tabIcon.ImageColor3 = active and Theme.AccentSoft or Theme.TextDim
		end
	end
	self._activeTab = entry.Page
	refreshPage(entry.Page)
end

function Window:GetActiveTab()
	return self._activeTab
end

function Window:CreateTab(name, iconName)
	if self._destroyed then
		return
	end
	local config = type(name) == "table" and name or nil
	if config then
		iconName = config.Icon or config.IconName
		name = config.Name or config.Title or "Tab"
	end
	name = tostring(name or "Tab")

	local page = make("ScrollingFrame", {
		Name = name .. "Page",
		Size = UDim2.fromScale(1, 1),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		CanvasSize = UDim2.fromOffset(0, 0),
		AutomaticCanvasSize = Enum.AutomaticSize.None,
		ScrollingDirection = Enum.ScrollingDirection.Y,
		ScrollBarThickness = 3,
		ScrollBarImageColor3 = Theme.Accent,
		ScrollBarImageTransparency = 0.2,
		Visible = false,
		ClipsDescendants = true
	}, self._content)
	page:SetAttribute("MikuPage", true)

	local holder = make("Frame", {
		Name = "Holder",
		Position = UDim2.fromOffset(18, 16),
		Size = UDim2.new(1, -36, 0, 0),
		AutomaticSize = Enum.AutomaticSize.Y,
		BackgroundTransparency = 1,
		BorderSizePixel = 0
	}, page)
	make("UIListLayout", {
		Padding = UDim.new(0, 8),
		SortOrder = Enum.SortOrder.LayoutOrder,
		HorizontalAlignment = Enum.HorizontalAlignment.Left,
		VerticalAlignment = Enum.VerticalAlignment.Top
	}, holder)

	local tabButton = make("TextButton", {
		Name = name .. "Tab",
		Size = UDim2.new(1, 0, 0, 42),
		BackgroundColor3 = Theme.SurfaceAlt,
		BackgroundTransparency = 0.55,
		AutoButtonColor = false,
		Text = "",
		BorderSizePixel = 0
	}, self._tabList)
	corner(tabButton, 9)
	stroke(tabButton, Theme.Stroke, 0.45)

	local indicator = make("Frame", {
		Name = "Indicator",
		Size = UDim2.fromOffset(3, 20),
		Position = UDim2.fromOffset(0, 11),
		BackgroundColor3 = Theme.Accent,
		BorderSizePixel = 0,
		Visible = false
	}, tabButton)
	corner(indicator, 2)

	local label = make("TextLabel", {
		Name = "Label",
		Position = UDim2.fromOffset(20, 0),
		Size = UDim2.new(1, -28, 1, 0),
		BackgroundTransparency = 1,
		Text = name,
		Font = Enum.Font.GothamMedium,
		TextSize = 14,
		TextColor3 = Theme.TextDim,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, tabButton)

	if iconName then
		local icon = Lib:GetCustomIcon(iconName)
		if icon then
			make("ImageLabel", {
				Name = "TabIcon",
				AnchorPoint = Vector2.new(0, 0.5),
				Position = UDim2.new(0, 10, 0.5, 0),
				Size = UDim2.fromOffset(18, 18),
				BackgroundTransparency = 1,
				ScaleType = Enum.ScaleType.Fit,
				Image = icon.Url or "",
				ImageColor3 = Theme.TextDim,
				ImageRectOffset = icon.ImageRectOffset or Vector2.zero,
				ImageRectSize = icon.ImageRectSize or Vector2.zero
			}, tabButton)

			label.Position = UDim2.fromOffset(36, 0)
			label.Size = UDim2.new(1, -42, 1, 0)
		end
	end

	local entry = {
		Name = name,
		Button = tabButton,
		Page = page,
		Indicator = indicator,
		Label = label,
		Active = false
	}
	table.insert(self._tabs, entry)

	tabButton.MouseEnter:Connect(function()
		if not entry.Active then
			tabButton.BackgroundColor3 = Theme.SurfaceHi
			tabButton.BackgroundTransparency = 0.42
		end
	end)
	tabButton.MouseLeave:Connect(function()
		if not entry.Active then
			tabButton.BackgroundColor3 = Theme.SurfaceAlt
			tabButton.BackgroundTransparency = 0.55
		end
	end)
	tabButton.MouseButton1Click:Connect(function()
		if not self._destroyed then
			self:SelectTab(entry)
		end
	end)

	if not self._activeTab then
		self:SelectTab(entry)
	end
	refreshPage(page)
	return page, tabButton
end

function Window:Notify(title, text, duration)
	return Lib:Notify(title, text, duration)
end

function Window:CreateWatermark(text)
	return Lib:CreateWatermark(text)
end

function Window:Watermark(text)
	return Lib:CreateWatermark(text)
end

function Window:termark(text)
	return Lib:CreateWatermark(text)
end

function Window:SetKeybind(key)
	if self._keybindConnection then
		self._keybindConnection:Disconnect()
		self._keybindConnection = nil
	end
	if key == nil or key == false then
		self._keybind = false
		return
	end
	self._keybind = key
	self._keybindConnection = UserInputService.InputBegan:Connect(function(input)
		if input.KeyCode == self._keybind and not UserInputService:GetFocusedTextBox() then
			self:Toggle()
		end
	end)
end

function Window:GetKeybind()
	return self._keybind
end

function Window:Destroy()
	if self._destroyed then
		return
	end
	self._destroyed = true
	self._visible = false

	if self._resizeApi then
		pcall(function()
			self._resizeApi.Destroy()
		end)
		self._resizeApi = nil
	end
	if self._dragApi then
		pcall(function()
			self._dragApi.Destroy()
		end)
		self._dragApi = nil
	end
	if self._keybindConnection then
		self._keybindConnection:Disconnect()
		self._keybindConnection = nil
	end

	self:_cancelTweens()

	for index = #Lib.Windows, 1, -1 do
		if Lib.Windows[index] == self then
			table.remove(Lib.Windows, index)
		end
	end

	local sharedTable = getShared(false)
	if sharedTable and sharedTable.MikuLibInstance == self then
		sharedTable.MikuLibInstance = nil
	end

	if self._screenGui then
		self._screenGui:Destroy()
		self._screenGui = nil
	end
end

function Lib:CreateWindow(config)
	config = type(config) == "table" and config or {}
	local sharedTable = getShared(true)
	if sharedTable then
		local old = sharedTable.MikuLibInstance
		if old and old ~= nil and not sharedTable.MikuLibKeepOldInstance and type(old.Destroy) == "function" then
			pcall(function()
				old:Destroy()
			end)
		end
	end

	local self = setmetatable({}, Window)
	self._tweens = {}
	self._tabs = {}
	self._visible = false
	self._minimized = false
	self._animToken = 0
	self._destroyed = false

	local viewport = getViewport()
	local requestedWidth = 1000
	local requestedHeight = 580
	if typeof(config.Size) == "UDim2" then
		if config.Size.X.Scale > 0 then
			requestedWidth = viewport.X * config.Size.X.Scale
		elseif config.Size.X.Offset > 0 then
			requestedWidth = config.Size.X.Offset
		end
		if config.Size.Y.Scale > 0 then
			requestedHeight = viewport.Y * config.Size.Y.Scale
		elseif config.Size.Y.Offset > 0 then
			requestedHeight = config.Size.Y.Offset
		end
	end
	if tonumber(config.Width) then
		requestedWidth = tonumber(config.Width)
	end
	if tonumber(config.Height) then
		requestedHeight = tonumber(config.Height)
	end

	local maxWidth = math.max(320, viewport.X - 40)
	local minWidth = math.min(360, maxWidth)
	local maxHeight = math.max(240, viewport.Y - 40)
	local minHeight = math.min(240, maxHeight)
	local width = math.clamp(requestedWidth, minWidth, maxWidth)
	local height = math.clamp(requestedHeight, minHeight, maxHeight)
	self._baseSize = Vector2.new(width, height)

	local screenGui = make("ScreenGui", {
		Name = "MikuLib",
		DisplayOrder = config.DisplayOrder or 9999,
		IgnoreGuiInset = true,
		ResetOnSpawn = false,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling
	}, PlayerGui)
	screenGui:SetAttribute("MikuAccent", true)
	screenGui:SetAttribute("MikuVersion", Lib.Version)
	self._screenGui = screenGui

	local startX = math.max(0, (viewport.X - width) / 2)
	local startY = math.max(0, (viewport.Y - height) / 2)
	local wrapper = make("Frame", {
		Name = "Window",
		AnchorPoint = Vector2.new(0, 0),
		Position = UDim2.fromOffset(startX, startY),
		Size = UDim2.fromOffset(width, height),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		ClipsDescendants = false,
		Active = false,
		Visible = false
	}, screenGui)
	self._wrapper = wrapper

	local root = make("CanvasGroup", {
		Name = "Root",
		Size = UDim2.fromScale(1, 1),
		BackgroundColor3 = Theme.Bg,
		BackgroundTransparency = 0,
		BorderSizePixel = 0,
		ClipsDescendants = true,
		Active = false
	}, wrapper)
	corner(root, 14)
	stroke(root, Theme.Stroke, 0.25)
	self._root = root

	local rootScale = make("UIScale", {
		Name = "Scale",
		Scale = 1
	}, root)
	self._rootScale = rootScale

	local topBar = make("Frame", {
		Name = "TopBar",
		Size = UDim2.new(1, 0, 0, 55),
		BackgroundColor3 = Theme.Surface,
		BorderSizePixel = 0
	}, root)
	self._topBar = topBar
	make("Frame", {
		Name = "Border",
		Position = UDim2.new(0, 0, 1, -1),
		Size = UDim2.new(1, 0, 0, 1),
		BackgroundColor3 = Theme.Stroke,
		BackgroundTransparency = 0.3,
		BorderSizePixel = 0
	}, topBar)

	local titleLabel = make("TextLabel", {
		Name = "Title",
		Position = UDim2.fromOffset(18, 8),
		Size = UDim2.new(1, -120, 0, 20),
		BackgroundTransparency = 1,
		Text = "MikuLib",
		Font = Enum.Font.GothamBold,
		TextSize = 15,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, topBar)
	self._titleLabel = titleLabel

	local subtitleLabel = make("TextLabel", {
		Name = "Subtitle",
		Position = UDim2.fromOffset(18, 29),
		Size = UDim2.new(1, -120, 0, 14),
		BackgroundTransparency = 1,
		Text = "v" .. Lib.Version,
		Font = Enum.Font.GothamMedium,
		TextSize = 11,
		TextColor3 = Theme.TextDim,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, topBar)
	self._subtitleLabel = subtitleLabel

	local minButton = make("TextButton", {
		Name = "MinimizeButton",
		Position = UDim2.new(1, -72, 0.5, -14),
		Size = UDim2.fromOffset(28, 28),
		BackgroundColor3 = Theme.Accent,
		BackgroundTransparency = 1,
		Text = "−",
		TextColor3 = Theme.TextDim,
		Font = Enum.Font.GothamBold,
		TextSize = 18,
		AutoButtonColor = false,
		BorderSizePixel = 0,
		ZIndex = 6
	}, topBar)
	corner(minButton, 7)
	minButton.MouseEnter:Connect(function()
		TweenService:Create(minButton, Fast, {
			BackgroundTransparency = 0.78,
			TextColor3 = Theme.AccentSoft
		}):Play()
	end)
	minButton.MouseLeave:Connect(function()
		TweenService:Create(minButton, Fast, {
			BackgroundTransparency = 1,
			TextColor3 = Theme.TextDim
		}):Play()
	end)
	minButton.MouseButton1Click:Connect(function()
		self:Minimize()
	end)

	local closeButton = make("TextButton", {
		Name = "CloseButton",
		Position = UDim2.new(1, -38, 0.5, -14),
		Size = UDim2.fromOffset(28, 28),
		BackgroundColor3 = Theme.Danger,
		BackgroundTransparency = 1,
		Text = "×",
		TextColor3 = Theme.TextDim,
		Font = Enum.Font.GothamBold,
		TextSize = 18,
		AutoButtonColor = false,
		BorderSizePixel = 0,
		ZIndex = 6
	}, topBar)
	corner(closeButton, 7)
	closeButton.MouseEnter:Connect(function()
		TweenService:Create(closeButton, Fast, {
			BackgroundTransparency = 0.82,
			TextColor3 = Theme.Danger
		}):Play()
	end)
	closeButton.MouseLeave:Connect(function()
		TweenService:Create(closeButton, Fast, {
			BackgroundTransparency = 1,
			TextColor3 = Theme.TextDim
		}):Play()
	end)
	closeButton.MouseButton1Click:Connect(function()
		self:Close()
	end)

	local dragArea = make("Frame", {
		Name = "DragArea",
		Position = UDim2.fromOffset(0, 0),
		Size = UDim2.new(1, -100, 0, 55),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		Active = true,
		ZIndex = 4
	}, topBar)
	self._dragArea = dragArea
	self._dragApi = Lib.newDraggable(wrapper, dragArea, false)

	local sidebar = make("Frame", {
		Name = "Sidebar",
		Position = UDim2.new(0, 0, 0, 55),
		Size = UDim2.new(0, 156, 1, -55),
		BackgroundColor3 = Theme.Surface,
		BorderSizePixel = 0
	}, root)
	self._sidebar = sidebar
	make("Frame", {
		Name = "Border",
		Position = UDim2.new(1, -1, 0, 0),
		Size = UDim2.new(0, 1, 1, 0),
		BackgroundColor3 = Theme.Stroke,
		BackgroundTransparency = 0.25,
		BorderSizePixel = 0
	}, sidebar)

	local tabList = make("Frame", {
		Name = "TabList",
		Position = UDim2.fromOffset(8, 10),
		Size = UDim2.new(1, -16, 1, -100),
		BackgroundTransparency = 1,
		BorderSizePixel = 0
	}, sidebar)
	make("UIListLayout", {
		Padding = UDim.new(0, 8),
		SortOrder = Enum.SortOrder.LayoutOrder,
		HorizontalAlignment = Enum.HorizontalAlignment.Left,
		VerticalAlignment = Enum.VerticalAlignment.Top
	}, tabList)
	self._tabList = tabList

	local profileCard = make("Frame", {
		Name = "ProfileCard",
		AnchorPoint = Vector2.new(0, 1),
		Position = UDim2.new(0, 8, 1, -10),
		Size = UDim2.new(1, -16, 0, 58),
		BackgroundColor3 = Theme.SurfaceAlt,
		BackgroundTransparency = 0.45,
		BorderSizePixel = 0
	}, sidebar)
	corner(profileCard, 10)
	stroke(profileCard, Theme.Stroke, 0.45)
	self._profileCard = profileCard

	local avatar = make("ImageLabel", {
		Name = "Avatar",
		Position = UDim2.fromOffset(9, 9),
		Size = UDim2.fromOffset(40, 40),
		BackgroundColor3 = Theme.SurfaceHi,
		BackgroundTransparency = 0,
		BorderSizePixel = 0,
		Image = "",
		ImageTransparency = 0,
		ScaleType = Enum.ScaleType.Crop,
		ClipsDescendants = true
	}, profileCard)
	corner(avatar, 20)
	stroke(avatar, Theme.Stroke, 0.5)
	self._avatar = avatar

	local profileName = make("TextLabel", {
		Name = "Name",
		Position = UDim2.fromOffset(58, 11),
		Size = UDim2.new(1, -68, 0, 18),
		BackgroundTransparency = 1,
		Text = LocalPlayer.DisplayName or LocalPlayer.Name,
		Font = Enum.Font.GothamBold,
		TextSize = 12,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, profileCard)
	self._profileName = profileName

	local profileTag = make("TextLabel", {
		Name = "Tag",
		Position = UDim2.fromOffset(58, 29),
		Size = UDim2.new(1, -68, 0, 16),
		BackgroundTransparency = 1,
		Text = "@" .. LocalPlayer.Name,
		Font = Enum.Font.GothamMedium,
		TextSize = 10,
		TextColor3 = Theme.AccentSoft,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, profileCard)
	self._profileTag = profileTag

	local content = make("Frame", {
		Name = "Content",
		Position = UDim2.new(0, 157, 0, 55),
		Size = UDim2.new(1, -157, 1, -55),
		BackgroundColor3 = Theme.Bg,
		BorderSizePixel = 0,
		ClipsDescendants = true
	}, root)
	self._content = content

	self._resizeApi = Lib.newResize(wrapper, function(newWidth, newHeight)
		if self._destroyed then
			return
		end
		self._baseSize = Vector2.new(newWidth, newHeight)
		for _, entry in ipairs(self._tabs) do
			refreshPage(entry.Page)
		end
		self:ClampPosition()
	end)

	self:SetTitle(config.Title or "MikuLib", config.Subtitle or ("v" .. Lib.Version))
	self:SetProfile({
		Name = LocalPlayer.DisplayName or LocalPlayer.Name,
		Tag = "@" .. LocalPlayer.Name
	})
	if type(config.Profile) == "table" then
		self:SetProfile(config.Profile)
	end

	task.spawn(function()
		local ok, image = pcall(function()
			return Players:GetUserThumbnailAsync(LocalPlayer.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size100x100)
		end)
		if ok and image and self._avatar and self._avatar.Parent then
			self._avatar.Image = image
		end
	end)

	local keybind = config.Keybind
	if keybind == nil then
		keybind = Enum.KeyCode.RightControl
	end
	self:SetKeybind(keybind)
	table.insert(Lib.Windows, self)

	local startVisible = config.StartVisible
	if startVisible == nil then
		startVisible = config.Visible
	end
	if startVisible == nil then
		startVisible = true
	end
	if startVisible then
		self:Open()
	else
		self._rootScale.Scale = 1
		self._root.GroupTransparency = 1
		self._wrapper.Visible = false
	end

	if sharedTable then
		sharedTable.MikuLibInstance = self
	end
	return self
end

function Lib:SetValue(control, value)
	local data = self.Controls[control]
	if data and data.SetValue then
		data.SetValue(value)
	end
end

function Lib:GetValue(control)
	local data = self.Controls[control]
	if data and data.GetValue then
		return data.GetValue()
	end
end

function Lib:SetVisible(control, visible)
	if control and control.Parent then
		control.Visible = visible == true
		local page = getPageFrom(control)
		refreshPage(page)
	end
end

function Lib:SetDisabled(control, disabled)
	local data = self.Controls[control]
	if data and data.SetDisabled then
		data.SetDisabled(disabled == true)
	elseif control then
		control:SetAttribute("MikuDisabled", disabled == true)
	end
end

function Lib:CreateSection(parent, text)
	local section, page = prepareControl(parent, 26, "Frame")
	make("TextLabel", {
		Name = "Title",
		Size = UDim2.new(1, 0, 0, 24),
		BackgroundTransparency = 1,
		Text = tostring(text or "Seção"),
		Font = Enum.Font.GothamBold,
		TextSize = 13,
		TextColor3 = Theme.AccentSoft,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, section)
	make("Frame", {
		Name = "Line",
		Position = UDim2.new(0, 0, 1, -1),
		Size = UDim2.new(1, 0, 0, 1),
		BackgroundColor3 = Theme.Stroke,
		BackgroundTransparency = 0.25,
		BorderSizePixel = 0
	}, section)
	refreshPage(page)
	return section
end

function Lib:CreateLabel(parent, text)
	local label, page = prepareControl(parent, 20, "TextLabel")
	label.Text = tostring(text or "")
	label.Font = Enum.Font.Gotham
	label.TextSize = 13
	label.TextColor3 = Theme.Text
	label.TextWrapped = true
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.TextYAlignment = Enum.TextYAlignment.Top
	label.AutomaticSize = Enum.AutomaticSize.Y
	label.Size = UDim2.new(1, 0, 0, 20)
	refreshPage(page)
	return label
end

function Lib:CreateDivider(parent)
	local divider, page = prepareControl(parent, 1, "Frame")
	divider.BackgroundColor3 = Theme.Stroke
	divider.BackgroundTransparency = 0.25
	refreshPage(page)
	return divider
end

function Lib:CreateButton(parent, text, callback)
	local button, page = prepareControl(parent, 38, "TextButton")
	button.BackgroundColor3 = Theme.SurfaceAlt
	button.BackgroundTransparency = 0.25
	button.Text = tostring(text or "Botão")
	button.Font = Enum.Font.GothamMedium
	button.TextSize = 14
	button.TextColor3 = Theme.Text
	button.AutoButtonColor = false
	corner(button, 9)
	stroke(button, Theme.Stroke, 0.4)
	button.MouseEnter:Connect(function()
		TweenService:Create(button, Fast, {
			BackgroundColor3 = Theme.SurfaceHi,
			BackgroundTransparency = 0.12
		}):Play()
	end)
	button.MouseLeave:Connect(function()
		TweenService:Create(button, Fast, {
			BackgroundColor3 = Theme.SurfaceAlt,
			BackgroundTransparency = 0.25
		}):Play()
	end)
	button.MouseButton1Click:Connect(function()
		safeCall(callback)
	end)
	refreshPage(page)
	return button
end

function Lib:CreateToggle(parent, text, default, callback)
	local config = type(text) == "table" and text or nil
	if config then
		text = config.Text or config.Name or "Toggle"
		default = config.Default
		callback = config.Callback
	end
	if type(default) == "function" and callback == nil then
		callback = default
		default = false
	end

	local row, page = prepareControl(parent, config and config.Description and 58 or 42, "TextButton")
	row.BackgroundColor3 = Theme.SurfaceAlt
	row.BackgroundTransparency = 1
	row.Text = ""
	row.AutoButtonColor = false
	row.BorderSizePixel = 0
	corner(row, 9)

	local icon
	if config and config.Icon then
		icon = make("ImageLabel", {
			Name = "Icon",
			Position = UDim2.fromOffset(10, 9),
			Size = UDim2.fromOffset(24, 24),
			BackgroundTransparency = 1,
			Image = tostring(config.Icon),
			ImageColor3 = Theme.AccentSoft,
			ScaleType = Enum.ScaleType.Fit
		}, row)
	end

	local leftOffset = icon and 42 or 12

	make("TextLabel", {
		Name = "Label",
		Position = UDim2.fromOffset(leftOffset, config and config.Description and 5 or 0),
		Size = UDim2.new(1, -leftOffset - 70, 0, config and config.Description and 20 or 42),
		BackgroundTransparency = 1,
		Text = tostring(text or "Toggle"),
		Font = Enum.Font.GothamMedium,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd
	}, row)

	if config and config.Description then
		make("TextLabel", {
			Name = "Description",
			Position = UDim2.fromOffset(leftOffset, 27),
			Size = UDim2.new(1, -leftOffset - 70, 0, 20),
			BackgroundTransparency = 1,
			Text = tostring(config.Description),
			Font = Enum.Font.Gotham,
			TextSize = 10,
			TextColor3 = Theme.TextDim,
			TextXAlignment = Enum.TextXAlignment.Left,
			TextTruncate = Enum.TextTruncate.AtEnd
		}, row)
	end

	local track = make("Frame", {
		Name = "Track",
		Position = UDim2.new(1, -52, 0.5, -11),
		Size = UDim2.fromOffset(40, 22),
		BackgroundColor3 = Theme.SurfaceHi,
		BorderSizePixel = 0
	}, row)
	corner(track, 11)
	stroke(track, Theme.Stroke, 0.5)

	local knob = make("Frame", {
		Name = "Knob",
		Position = UDim2.new(0, 3, 0.5, 0),
		Size = UDim2.fromOffset(16, 16),
		AnchorPoint = Vector2.new(0, 0.5),
		BackgroundColor3 = Theme.Text,
		BorderSizePixel = 0
	}, track)
	corner(knob, 8)

	local active = default == true
	local disabled = false

	local function apply(state, fire)
		active = state == true
		row:SetAttribute("MikuValue", active)

		TweenService:Create(track, Fast, {
			BackgroundColor3 = active and Theme.Accent or Theme.SurfaceHi
		}):Play()

		TweenService:Create(knob, Normal, {
			Position = active and UDim2.new(1, -19, 0.5, 0) or UDim2.new(0, 3, 0.5, 0)
		}):Play()

		if fire then
			safeCall(callback, active)
		end
	end

	row.MouseEnter:Connect(function()
		if not disabled then
			TweenService:Create(row, Fast, {
				BackgroundTransparency = 0.82
			}):Play()
		end
	end)

	row.MouseLeave:Connect(function()
		TweenService:Create(row, Fast, {
			BackgroundTransparency = 1
		}):Play()
	end)

	row.MouseButton1Click:Connect(function()
		if not disabled then
			apply(not active, true)
		end
	end)

	apply(active, false)

	registerControl(row, {
		SetValue = function(value)
			apply(value, true)
		end,
		GetValue = function()
			return active
		end,
		SetDisabled = function(value)
			disabled = value
			row.Active = not value
			row.TextTransparency = value and 0.5 or 0
		end
	})

	refreshPage(page)
	return row
end

function Lib:CreateCheckbox(parent, text, default, callback)
	local row, page = prepareControl(parent, 38, "TextButton")
	row.BackgroundTransparency = 1
	row.Text = ""
	row.AutoButtonColor = false

	make("TextLabel", {
		Position = UDim2.fromOffset(12, 0),
		Size = UDim2.new(1, -48, 1, 0),
		BackgroundTransparency = 1,
		Text = tostring(text or "Checkbox"),
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left
	}, row)

	local box = make("Frame", {
		Position = UDim2.new(1, -32, 0.5, -10),
		Size = UDim2.fromOffset(20, 20),
		BackgroundColor3 = Theme.SurfaceHi,
		BorderSizePixel = 0
	}, row)
	corner(box, 5)
	stroke(box, Theme.Stroke, 0.35)

	local mark = make("TextLabel", {
		Size = UDim2.fromScale(1, 1),
		BackgroundTransparency = 1,
		Text = "✓",
		TextColor3 = Theme.Text,
		TextSize = 14,
		Font = Enum.Font.GothamBold,
		Visible = default == true
	}, box)

	local value = default == true

	local function apply(state, fire)
		value = state == true
		mark.Visible = value
		box.BackgroundColor3 = value and Theme.Accent or Theme.SurfaceHi
		row:SetAttribute("MikuValue", value)

		if fire then
			safeCall(callback, value)
		end
	end

	row.MouseButton1Click:Connect(function()
		apply(not value, true)
	end)

	apply(value, false)

	registerControl(row, {
		SetValue = function(v) apply(v, true) end,
		GetValue = function() return value end
	})

	refreshPage(page)
	return row
end

function Lib:CreateSlider(parent, text, minValue, maxValue, defaultValue, callback)
	if type(defaultValue) == "function" and callback == nil then
		callback = defaultValue
		defaultValue = minValue
	end
	local frame, page = prepareControl(parent, 52, "Frame")
	make("TextLabel", {
		Name = "Label",
		Position = UDim2.fromOffset(0, 0),
		Size = UDim2.new(1, -60, 0, 18),
		BackgroundTransparency = 1,
		Text = tostring(text or "Slider"),
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, frame)
	local valueLabel = make("TextLabel", {
		Name = "Value",
		Position = UDim2.new(1, -60, 0, 0),
		Size = UDim2.fromOffset(60, 18),
		BackgroundTransparency = 1,
		Text = "",
		Font = Enum.Font.GothamMedium,
		TextSize = 12,
		TextColor3 = Theme.AccentSoft,
		TextXAlignment = Enum.TextXAlignment.Right,
		BorderSizePixel = 0
	}, frame)
	local track = make("TextButton", {
		Name = "Track",
		Position = UDim2.fromOffset(0, 32),
		Size = UDim2.new(1, 0, 0, 8),
		BackgroundColor3 = Theme.SurfaceHi,
		Text = "",
		AutoButtonColor = false,
		BorderSizePixel = 0
	}, frame)
	corner(track, 4)
	local fill = make("Frame", {
		Name = "Fill",
		Size = UDim2.new(0, 0, 1, 0),
		BackgroundColor3 = Theme.Accent,
		BorderSizePixel = 0,
		Active = false
	}, track)
	corner(fill, 4)
	local knob = make("Frame", {
		Name = "Knob",
		Position = UDim2.new(0, 0, 0.5, 0),
		Size = UDim2.fromOffset(14, 14),
		AnchorPoint = Vector2.new(0.5, 0.5),
		BackgroundColor3 = Theme.Text,
		BorderSizePixel = 0,
		Active = false
	}, track)
	corner(knob, 7)
	stroke(knob, Theme.Stroke, 0.4)

	local minNumber = tonumber(minValue) or 0
	local maxNumber = tonumber(maxValue) or 100
	if maxNumber <= minNumber then
		maxNumber = minNumber + 1
	end
	local value = math.clamp(tonumber(defaultValue) or minNumber, minNumber, maxNumber)
	local dragging = false
	local moveConnection
	local endConnection

	local function apply(nextValue, fire)
		value = math.clamp(tonumber(nextValue) or minNumber, minNumber, maxNumber)
		local fraction = (value - minNumber) / (maxNumber - minNumber)
		fill.Size = UDim2.new(fraction, 0, 1, 0)
		knob.Position = UDim2.new(fraction, 0, 0.5, 0)
		valueLabel.Text = formatValue(value)
		frame:SetAttribute("MikuValue", value)
		if fire then
			safeCall(callback, value)
		end
	end

	local function stop()
		dragging = false
		if moveConnection then
			moveConnection:Disconnect()
			moveConnection = nil
		end
		if endConnection then
			endConnection:Disconnect()
			endConnection = nil
		end
	end

	local function fromX(positionX)
		local relative = math.clamp((positionX - track.AbsolutePosition.X) / math.max(1, track.AbsoluteSize.X), 0, 1)
		return minNumber + (maxNumber - minNumber) * relative
	end

	track.InputBegan:Connect(function(input)
		if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then
			return
		end
		stop()
		dragging = true
		apply(fromX(input.Position.X), true)
		moveConnection = UserInputService.InputChanged:Connect(function(changed)
			if dragging and (changed.UserInputType == Enum.UserInputType.MouseMovement or changed.UserInputType == Enum.UserInputType.Touch) then
				apply(fromX(changed.Position.X), true)
			end
		end)
		endConnection = UserInputService.InputEnded:Connect(function(changed)
			if changed.UserInputType == Enum.UserInputType.MouseButton1 or changed.UserInputType == Enum.UserInputType.Touch then
				stop()
			end
		end)
	end)

	apply(value, false)

	registerControl(frame, {
		SetValue = function(v) apply(v, true) end,
		GetValue = function() return value end
	})

	refreshPage(page)
	return frame
end

function Lib:CreateTextBox(parent, text, placeholder, defaultValue, callback)
	if type(defaultValue) == "function" and callback == nil then
		callback = defaultValue
		defaultValue = ""
	end
	if type(placeholder) == "function" and callback == nil then
		callback = placeholder
		placeholder = "Digite"
	end
	local frame, page = prepareControl(parent, 42, "Frame")
	make("TextLabel", {
		Name = "Label",
		Position = UDim2.fromOffset(0, 0),
		Size = UDim2.new(0.42, 0, 1, 0),
		BackgroundTransparency = 1,
		Text = tostring(text or "TextBox"),
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, frame)
	local box = make("TextBox", {
		Name = "Input",
		Position = UDim2.new(0.42, 0, 0, 7),
		Size = UDim2.new(0.58, 0, 0, 28),
		BackgroundColor3 = Theme.SurfaceHi,
		BackgroundTransparency = 0.08,
		BorderSizePixel = 0,
		Text = tostring(defaultValue or ""),
		PlaceholderText = tostring(placeholder or "Digite"),
		PlaceholderColor3 = Theme.TextDim,
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		ClearTextOnFocus = false,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd
	}, frame)
	corner(box, 8)
	stroke(box, Theme.Stroke, 0.4)
	make("UIPadding", {
		PaddingLeft = UDim.new(0, 8),
		PaddingRight = UDim.new(0, 8)
	}, box)
	box.FocusLost:Connect(function()
		safeCall(callback, box.Text)
	end)

	registerControl(box, {
		SetValue = function(v)
			box.Text = tostring(v or "")
			safeCall(callback, box.Text)
		end,
		GetValue = function()
			return box.Text
		end
	})

	refreshPage(page)
	return box
end

function Lib:CreateDropdown(parent, text, options, default, callback)
	local config = type(text) == "table" and text or nil
	if config then
		text = config.Text or config.Name
		options = config.Values or config.Options
		default = config.Default
		callback = config.Callback
	end
	if type(default) == "function" and callback == nil then
		callback = default
		default = nil
	end

	options = type(options) == "table" and options or {}

	local multi = config and config.Multi == true
	local searchable = config and config.Searchable == true
	local selected = {}

	if multi then
		if type(default) == "table" then
			for _, value in ipairs(default) do
				selected[value] = true
			end
		end
	else
		selected = default
		if selected == nil then
			selected = options[1]
		end
	end

	local control, page = prepareControl(parent, 42, "Frame")

	local row = make("TextButton", {
		Name = "Dropdown",
		Size = UDim2.new(1, 0, 0, 42),
		BackgroundColor3 = Theme.SurfaceAlt,
		BackgroundTransparency = 0.25,
		Text = "",
		AutoButtonColor = false,
		BorderSizePixel = 0
	}, control)
	corner(row, 9)
	stroke(row, Theme.Stroke, 0.4)

	make("TextLabel", {
		Position = UDim2.fromOffset(12, 0),
		Size = UDim2.new(0.48, -12, 1, 0),
		BackgroundTransparency = 1,
		Text = tostring(text or "Dropdown"),
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd
	}, row)

	local valueLabel = make("TextLabel", {
		Position = UDim2.new(0.5, 0, 0, 0),
		Size = UDim2.new(0.5, -30, 1, 0),
		BackgroundTransparency = 1,
		Text = "",
		Font = Enum.Font.GothamMedium,
		TextSize = 11,
		TextColor3 = Theme.AccentSoft,
		TextXAlignment = Enum.TextXAlignment.Right,
		TextTruncate = Enum.TextTruncate.AtEnd
	}, row)

	local arrow = make("TextLabel", {
		Position = UDim2.new(1, -22, 0, 0),
		Size = UDim2.fromOffset(14, 42),
		BackgroundTransparency = 1,
		Text = "⌄",
		Font = Enum.Font.GothamBold,
		TextSize = 14,
		TextColor3 = Theme.TextDim
	}, row)

	local list = make("Frame", {
		Name = "Options",
		Position = UDim2.fromOffset(0, 47),
		Size = UDim2.new(1, 0, 0, 0),
		BackgroundColor3 = Theme.Surface,
		BorderSizePixel = 0,
		ClipsDescendants = true,
		Visible = false,
		ZIndex = 20
	}, control)
	corner(list, 9)
	stroke(list, Theme.Stroke, 0.35)

	local searchBox
	if searchable then
		searchBox = make("TextBox", {
			Name = "Search",
			Position = UDim2.fromOffset(6, 6),
			Size = UDim2.new(1, -12, 0, 30),
			BackgroundColor3 = Theme.SurfaceAlt,
			BorderSizePixel = 0,
			Text = "",
			PlaceholderText = "Pesquisar...",
			PlaceholderColor3 = Theme.TextDim,
			TextColor3 = Theme.Text,
			Font = Enum.Font.Gotham,
			TextSize = 12,
			ClearTextOnFocus = false,
			ZIndex = 22
		}, list)
		corner(searchBox, 6)
	end

	local optionsHolder = make("ScrollingFrame", {
		Name = "OptionsHolder",
		Position = UDim2.fromOffset(4, searchable and 42 or 4),
		Size = UDim2.new(1, -8, 1, searchable and -46 or -8),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		ScrollBarThickness = 3,
		ScrollBarImageColor3 = Theme.Accent,
		CanvasSize = UDim2.fromOffset(0, 0),
		ZIndex = 21
	}, list)

	local optionLayout = make("UIListLayout", {
		Padding = UDim.new(0, 3),
		SortOrder = Enum.SortOrder.LayoutOrder
	}, optionsHolder)

	optionLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		optionsHolder.CanvasSize = UDim2.fromOffset(0, optionLayout.AbsoluteContentSize.Y + 5)
	end)

	local opened = false
	local disabled = false
	local optionButtons = {}
	local setOpen

	local function getText()
		if multi then
			local values = {}
			for _, option in ipairs(options) do
				if selected[option] then
					table.insert(values, tostring(option))
				end
			end
			return #values > 0 and table.concat(values, ", ") or "Nenhum"
		end
		return selected ~= nil and tostring(selected) or "Nenhum"
	end

	local function updateLabel()
		valueLabel.Text = getText()
	end

	local function updateOptions()
		for _, child in ipairs(optionsHolder:GetChildren()) do
			if child:IsA("TextButton") then
				child:Destroy()
			end
		end

		optionButtons = {}

		for index, option in ipairs(options) do
			local isSelected = multi and selected[option] or selected == option

			local optionButton = make("TextButton", {
				Name = "Option_" .. index,
				Size = UDim2.new(1, -4, 0, 30),
				BackgroundColor3 = isSelected and Theme.SurfaceHi or Theme.Surface,
				BackgroundTransparency = isSelected and 0.1 or 1,
				BorderSizePixel = 0,
				Text = tostring(option),
				Font = Enum.Font.Gotham,
				TextSize = 12,
				TextColor3 = isSelected and Theme.AccentSoft or Theme.Text,
				TextXAlignment = Enum.TextXAlignment.Left,
				AutoButtonColor = false,
				LayoutOrder = index,
				ZIndex = 22
			}, optionsHolder)

			make("UIPadding", {
				PaddingLeft = UDim.new(0, 9)
			}, optionButton)
			corner(optionButton, 6)

			optionButton.MouseButton1Click:Connect(function()
				if disabled then
					return
				end
				if multi then
					selected[option] = not selected[option] or nil
					updateLabel()
					updateOptions()
					local values = {}
					for _, item in ipairs(options) do
						if selected[item] then
							table.insert(values, item)
						end
					end
					control:SetAttribute("MikuValue", #values)
					safeCall(callback, values)
				else
					selected = option
					updateLabel()
					setOpen(false)
					safeCall(callback, option)
					updateOptions()
				end
			end)

			optionButton.MouseEnter:Connect(function()
				TweenService:Create(optionButton, Fast, {
					BackgroundTransparency = 0.2
				}):Play()
			end)

			optionButton.MouseLeave:Connect(function()
				local active = multi and selected[option] or selected == option
				TweenService:Create(optionButton, Fast, {
					BackgroundTransparency = active and 0.1 or 1
				}):Play()
			end)

			optionButtons[#optionButtons + 1] = optionButton
		end

		updateLabel()
	end

	setOpen = function(value)
		opened = value
		list.Visible = true

		local count = #options
		local height = opened and math.min(count * 33 + 10 + (searchable and 42 or 0), 190) or 0

		TweenService:Create(list, Normal, {
			Size = UDim2.new(1, 0, 0, height)
		}):Play()

		TweenService:Create(arrow, Fast, {
			Rotation = opened and 180 or 0
		}):Play()

		if not opened then
			task.delay(0.21, function()
				if not opened and list.Parent then
					list.Visible = false
				end
			end)
		end

		control.Size = UDim2.new(1, 0, 0, opened and 48 + height or 42)
		refreshPage(page)
	end

	row.MouseButton1Click:Connect(function()
		if not disabled then
			setOpen(not opened)
		end
	end)

	if searchBox then
		searchBox:GetPropertyChangedSignal("Text"):Connect(function()
			local query = searchBox.Text:lower()
			for _, button in ipairs(optionButtons) do
				button.Visible = query == "" or button.Text:lower():find(query, 1, true) ~= nil
			end
		end)
	end

	updateOptions()

	local function setValue(value, fire)
		if multi then
			selected = {}
			if type(value) == "table" then
				for _, item in ipairs(value) do
					for _, option in ipairs(options) do
						if option == item then
							selected[item] = true
						end
					end
				end
			end
		else
			for _, option in ipairs(options) do
				if option == value then
					selected = value
					break
				end
			end
		end

		updateOptions()

		if fire then
			if multi then
				local values = {}
				for _, option in ipairs(options) do
					if selected[option] then
						table.insert(values, option)
					end
				end
				safeCall(callback, values)
			else
				safeCall(callback, selected)
			end
		end
	end

	registerControl(control, {
		SetValue = function(value) setValue(value, true) end,
		GetValue = function()
			if multi then
				local values = {}
				for _, option in ipairs(options) do
					if selected[option] then
						table.insert(values, option)
					end
				end
				return values
			end
			return selected
		end,
		SetDisabled = function(value)
			disabled = value
			row.Active = not value
			row.BackgroundTransparency = value and 0.65 or 0.25
		end
	})

	control.Set = function(_, value)
		setValue(value, true)
	end
	control.Get = function()
		return Lib:GetValue(control)
	end
	control.SetOptions = function(_, newOptions)
		options = type(newOptions) == "table" and newOptions or {}
		updateOptions()
		refreshPage(page)
	end
	control.GetOptions = function()
		return options
	end

	refreshPage(page)
	return control
end

function Lib:CreateKeybind(parent, text, defaultKey, callback)
	local config = type(text) == "table" and text or nil
	if config then
		text = config.Text or config.Name
		defaultKey = config.Default
		callback = config.Callback
	end

	local frame, page = prepareControl(parent, 42, "Frame")

	make("TextLabel", {
		Position = UDim2.fromOffset(12, 0),
		Size = UDim2.new(1, -120, 1, 0),
		BackgroundTransparency = 1,
		Text = tostring(text or "Keybind"),
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left
	}, frame)

	local button = make("TextButton", {
		Position = UDim2.new(1, -105, 0.5, -14),
		Size = UDim2.fromOffset(93, 28),
		BackgroundColor3 = Theme.SurfaceHi,
		BorderSizePixel = 0,
		Text = "",
		Font = Enum.Font.GothamMedium,
		TextSize = 11,
		TextColor3 = Theme.AccentSoft,
		AutoButtonColor = false
	}, frame)
	corner(button, 7)
	stroke(button, Theme.Stroke, 0.4)

	local currentKey = defaultKey or Enum.KeyCode.RightShift
	local listening = false
	local disabled = false

	local function update()
		button.Text = currentKey and currentKey.Name or "None"
		button.TextColor3 = Theme.AccentSoft
		frame:SetAttribute("MikuKeybind", button.Text)
	end

	update()

	button.MouseButton1Click:Connect(function()
		if disabled then
			return
		end
		listening = true
		button.Text = "Pressione..."
		button.TextColor3 = Theme.Success
	end)

	local connection = UserInputService.InputBegan:Connect(function(input, processed)
		if listening then
			if input.UserInputType ~= Enum.UserInputType.Keyboard then
				return
			end
			listening = false

			if input.KeyCode == Enum.KeyCode.Escape then
				update()
				return
			end
			if input.KeyCode == Enum.KeyCode.Backspace then
				currentKey = nil
				update()
				return
			end

			currentKey = input.KeyCode
			update()
			return
		end

		if disabled or processed or UserInputService:GetFocusedTextBox() then
			return
		end

		if currentKey and input.KeyCode == currentKey then
			safeCall(callback, currentKey)
		end
	end)

	registerControl(frame, {
		SetValue = function(key)
			if typeof(key) == "EnumItem" and key.EnumType == Enum.KeyCode then
				currentKey = key
				update()
			end
		end,
		GetValue = function()
			return currentKey
		end,
		SetDisabled = function(value)
			disabled = value
			button.TextTransparency = value and 0.5 or 0
		end
	})

	frame.Destroying:Connect(function()
		connection:Disconnect()
	end)

	refreshPage(page)
	return frame
end

function Lib:CreateColorPicker(parent, text, default, callback)
	local frame, page = prepareControl(parent, 42, "Frame")

	make("TextLabel", {
		Position = UDim2.fromOffset(12, 0),
		Size = UDim2.new(1, -65, 1, 0),
		BackgroundTransparency = 1,
		Text = tostring(text or "Color"),
		Font = Enum.Font.Gotham,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left
	}, frame)

	local color = typeof(default) == "Color3" and default or Theme.Accent
	local hue, saturation, value = color:ToHSV()

	local button = make("TextButton", {
		Position = UDim2.new(1, -48, 0.5, -13),
		Size = UDim2.fromOffset(36, 26),
		BackgroundColor3 = color,
		BorderSizePixel = 0,
		Text = "",
		AutoButtonColor = false
	}, frame)
	corner(button, 7)
	stroke(button, Theme.Stroke, 0.35)

	local popup = make("Frame", {
		Position = UDim2.new(1, -190, 1, 5),
		Size = UDim2.fromOffset(190, 0),
		BackgroundColor3 = Theme.Surface,
		BorderSizePixel = 0,
		Visible = false,
		ClipsDescendants = true,
		ZIndex = 30
	}, frame)
	corner(popup, 9)
	stroke(popup, Theme.Stroke, 0.35)

	local function updateColor(fire)
		color = Color3.fromHSV(hue, saturation, value)
		button.BackgroundColor3 = color
		frame:SetAttribute("MikuColor", color:ToHex())

		if fire then
			safeCall(callback, color)
		end
	end

	local function createColorSlider(name, y, initial, changed)
		make("TextLabel", {
			Position = UDim2.fromOffset(9, y),
			Size = UDim2.fromOffset(42, 20),
			BackgroundTransparency = 1,
			Text = name,
			TextColor3 = Theme.TextDim,
			Font = Enum.Font.GothamMedium,
			TextSize = 10,
			TextXAlignment = Enum.TextXAlignment.Left,
			ZIndex = 31
		}, popup)

		local track = make("TextButton", {
			Position = UDim2.fromOffset(52, y + 5),
			Size = UDim2.new(1, -62, 0, 10),
			BackgroundColor3 = Theme.SurfaceHi,
			BorderSizePixel = 0,
			Text = "",
			AutoButtonColor = false,
			ZIndex = 31
		}, popup)
		corner(track, 5)

		local fill = make("Frame", {
			Size = UDim2.new(initial, 0, 1, 0),
			BackgroundColor3 = Theme.Accent,
			BorderSizePixel = 0,
			ZIndex = 32
		}, track)
		corner(fill, 5)

		local dragging = false
		local moveConnection
		local endConnection

		local function setFromX(x)
			local fraction = math.clamp((x - track.AbsolutePosition.X) / math.max(1, track.AbsoluteSize.X), 0, 1)
			fill.Size = UDim2.new(fraction, 0, 1, 0)
			changed(fraction)
			updateColor(true)
		end

		track.InputBegan:Connect(function(input)
			if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then
				return
			end
			dragging = true
			setFromX(input.Position.X)

			moveConnection = UserInputService.InputChanged:Connect(function(changedInput)
				if dragging and (changedInput.UserInputType == Enum.UserInputType.MouseMovement or changedInput.UserInputType == Enum.UserInputType.Touch) then
					setFromX(changedInput.Position.X)
				end
			end)

			endConnection = UserInputService.InputEnded:Connect(function(changedInput)
				if changedInput.UserInputType == Enum.UserInputType.MouseButton1 or changedInput.UserInputType == Enum.UserInputType.Touch then
					dragging = false
					if moveConnection then moveConnection:Disconnect() moveConnection = nil end
					if endConnection then endConnection:Disconnect() endConnection = nil end
				end
			end)
		end)
	end

	createColorSlider("Hue", 12, hue, function(v) hue = v end)
	createColorSlider("Sat", 42, saturation, function(v) saturation = v end)
	createColorSlider("Value", 72, value, function(v) value = v end)

	button.MouseButton1Click:Connect(function()
		popup.Visible = not popup.Visible
		TweenService:Create(popup, Normal, {
			Size = UDim2.fromOffset(190, popup.Visible and 112 or 0)
		}):Play()
	end)

	registerControl(frame, {
		SetValue = function(v)
			if typeof(v) == "Color3" then
				color = v
				hue, saturation, value = color:ToHSV()
				updateColor(true)
			end
		end,
		GetValue = function()
			return color
		end
	})

	updateColor(false)
	refreshPage(page)
	return frame
end

function Lib:CreateIcon(parent, iconName, size, callback)
	local host = resolveHost(parent)
	local page = getPageFrom(host)
	local icon = self:GetCustomIcon(iconName)
	local isButton = type(callback) == "function"
	local object = make(isButton and "ImageButton" or "ImageLabel", {
		Name = "Icon",
		Size = typeof(size) == "UDim2" and size or UDim2.fromOffset(32, 32),
		BackgroundTransparency = 1,
		BorderSizePixel = 0,
		ScaleType = Enum.ScaleType.Fit,
		AutoButtonColor = false,
		LayoutOrder = host and nextLayoutOrder(host) or 0
	}, host)

	if icon then
		if icon.FontFace and icon.Text then
			object:Destroy()
			object = make(isButton and "TextButton" or "TextLabel", {
				Name = "Icon",
				Size = typeof(size) == "UDim2" and size or UDim2.fromOffset(32, 32),
				BackgroundTransparency = 1,
				BorderSizePixel = 0,
				FontFace = icon.FontFace,
				Text = icon.Text,
				TextColor3 = Theme.Text,
				TextSize = 22,
				AutoButtonColor = false,
				LayoutOrder = host and nextLayoutOrder(host) or 0
			}, host)
		else
			object.Image = icon.Url or ""
			object.ImageColor3 = Theme.Text
			object.ImageRectOffset = icon.ImageRectOffset or Vector2.zero
			object.ImageRectSize = icon.ImageRectSize or Vector2.zero
		end
	end

	if isButton then
		object.MouseButton1Click:Connect(function()
			safeCall(callback)
		end)
	end

	refreshPage(page)
	return object
end

function Lib:CreateIconButton(parent, iconName, size, callback)
	return self:CreateIcon(parent, iconName, size, callback)
end

local function ensureNotifyGui()
	if Lib._notifyGui and Lib._notifyGui.Parent then
		return Lib._notifyStack
	end
	local gui = make("ScreenGui", {
		Name = "MikuNotify",
		DisplayOrder = 10000,
		IgnoreGuiInset = true,
		ResetOnSpawn = false,
		ZIndexBehavior = Enum.ZIndexBehavior.Sibling
	}, PlayerGui)
	local stack = make("Frame", {
		Name = "Stack",
		AnchorPoint = Vector2.new(1, 1),
		Position = UDim2.new(1, -16, 1, -16),
		Size = UDim2.new(0, 300, 0, 0),
		AutomaticSize = Enum.AutomaticSize.Y,
		BackgroundTransparency = 1,
		BorderSizePixel = 0
	}, gui)
	make("UIListLayout", {
		Padding = UDim.new(0, 8),
		SortOrder = Enum.SortOrder.LayoutOrder,
		HorizontalAlignment = Enum.HorizontalAlignment.Right,
		VerticalAlignment = Enum.VerticalAlignment.Top
	}, stack)
	Lib._notifyGui = gui
	Lib._notifyStack = stack
	Lib._notifyCount = 0
	return stack
end

function Lib:Notify(title, text, duration)
	if type(title) == "table" then
		local config = title
		title = config.Title or config.title or "MikuLib"
		text = config.Text or config.text or config.Content or config.content or ""
		duration = config.Duration or config.duration or 4
	end
	title = tostring(title or "MikuLib")
	text = tostring(text or "")
	duration = tonumber(duration) or 4
	local stack = ensureNotifyGui()
	Lib._notifyCount = Lib._notifyCount + 1
	local card = make("CanvasGroup", {
		Name = "Notification",
		Size = UDim2.new(0, 300, 0, 64),
		BackgroundColor3 = Theme.Surface,
		BackgroundTransparency = 0,
		BorderSizePixel = 0,
		GroupTransparency = 1,
		ClipsDescendants = true,
		LayoutOrder = Lib._notifyCount
	}, stack)
	corner(card, 12)
	stroke(card, Theme.Stroke, 0.3)
	local scale = make("UIScale", {
		Name = "Scale",
		Scale = 0.96
	}, card)
	make("Frame", {
		Name = "Accent",
		Size = UDim2.new(0, 4, 1, 0),
		BackgroundColor3 = Theme.Accent,
		BorderSizePixel = 0
	}, card)
	make("TextLabel", {
		Name = "Title",
		Position = UDim2.fromOffset(14, 9),
		Size = UDim2.new(1, -28, 0, 18),
		BackgroundTransparency = 1,
		Text = title,
		Font = Enum.Font.GothamBold,
		TextSize = 13,
		TextColor3 = Theme.Text,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextTruncate = Enum.TextTruncate.AtEnd,
		BorderSizePixel = 0
	}, card)
	make("TextLabel", {
		Name = "Text",
		Position = UDim2.fromOffset(14, 28),
		Size = UDim2.new(1, -28, 0, 26),
		BackgroundTransparency = 1,
		Text = text,
		Font = Enum.Font.Gotham,
		TextSize = 12,
		TextColor3 = Theme.TextDim,
		TextXAlignment = Enum.TextXAlignment.Left,
		TextYAlignment = Enum.TextYAlignment.Top,
		TextWrapped = true,
		BorderSizePixel = 0
	}, card)
	local progressBackground = make("Frame", {
		Name = "Progress",
		Position = UDim2.new(0, 0, 1, -3),
		Size = UDim2.new(1, 0, 0, 3),
		BackgroundColor3 = Theme.SurfaceHi,
		BorderSizePixel = 0
	}, card)
	local progress = make("Frame", {
		Name = "Fill",
		Size = UDim2.new(1, 0, 1, 0),
		BackgroundColor3 = Theme.Accent,
		BorderSizePixel = 0
	}, progressBackground)
	TweenService:Create(card, TweenInfo.new(0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
		GroupTransparency = 0
	}):Play()
	TweenService:Create(scale, TweenInfo.new(0.26, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
		Scale = 1
	}):Play()
	TweenService:Create(progress, TweenInfo.new(math.max(0.1, duration), Enum.EasingStyle.Linear), {
		Size = UDim2.new(0, 0, 1, 0)
	}):Play()
	task.delay(math.max(0.1, duration), function()
		if not card.Parent then
			return
		end
		local fade = TweenService:Create(card, TweenInfo.new(0.2, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
			GroupTransparency = 1
		})
		fade:Play()
		fade.Completed:Connect(function()
			if card.Parent then
				card:Destroy()
			end
		end)
	end)
	return card
end

local function ensureWatermarkGui()
	if Lib._watermarkGui and Lib._watermarkGui.Parent then
		return Lib._watermarkGui
	end
	local old = PlayerGui:FindFirstChild("MikuWatermark")
	if old then
		old:Destroy()
	end
	local gui = Instance.new("ScreenGui")
	gui.Name = "MikuWatermark"
	gui.DisplayOrder = 10001
	gui.IgnoreGuiInset = true
	gui.ResetOnSpawn = false
	gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
	gui.Parent = PlayerGui

	Lib._watermarkGui = gui
	return gui
end

function Lib:CreateWatermark(config)
	if type(config) ~= "table" then
		config = {
			Text = tostring(config or "MikuLib")
		}
	end

	local gui = ensureWatermarkGui()
	local old = gui:FindFirstChild("Watermark")
	if old then
		old:Destroy()
	end

	local frame = Instance.new("Frame")
	frame.Name = "Watermark"
	frame.Position = config.Position or UDim2.fromOffset(16, 16)
	frame.Size = UDim2.fromOffset(220, 32)
	frame.BackgroundColor3 = Color3.fromRGB(20, 20, 23)
	frame.BackgroundTransparency = 0
	frame.BorderSizePixel = 0
	frame.Active = true
	frame.Visible = true
	frame.Parent = gui

	corner(frame, 8)

	local label = Instance.new("TextLabel")
	label.Name = "Text"
	label.Position = UDim2.fromOffset(12, 0)
	label.Size = UDim2.new(1, -24, 1, 0)
	label.BackgroundTransparency = 1
	label.BorderSizePixel = 0
	label.Text = tostring(config.Text or "MikuLib")
	label.Font = Enum.Font.GothamMedium
	label.TextSize = 12
	label.TextColor3 = Color3.fromRGB(240, 240, 245)
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.TextYAlignment = Enum.TextYAlignment.Center
	label.TextTruncate = Enum.TextTruncate.AtEnd
	label.Parent = frame

	local drag
	if config.Draggable == true and type(Lib.newDraggable) == "function" then
		local success, result = pcall(function()
			return Lib.newDraggable(frame, frame, false)
		end)
		if success then
			drag = result
		end
	end

	local api = {}
	local destroyed = false

	function api.SetText(selfOrValue, maybeValue)
		if destroyed then return end
		local value = maybeValue
		if value == nil then
			value = selfOrValue
		end
		label.Text = tostring(value or "")
	end

	function api.SetVisible(selfOrValue, maybeValue)
		if destroyed then return end
		local value = maybeValue
		if value == nil then
			value = selfOrValue
		end
		frame.Visible = value == true
	end

	function api.Destroy()
		if destroyed then return end
		destroyed = true

		if drag then
			pcall(function()
				if type(drag) == "table" and type(drag.Destroy) == "function" then
					drag:Destroy()
				elseif typeof(drag) == "RBXScriptConnection" then
					drag:Disconnect()
				elseif type(drag) == "function" then
					drag()
				end
			end)
		end

		for i = #Lib._watermarks, 1, -1 do
			if Lib._watermarks[i] == api then
				table.remove(Lib._watermarks, i)
			end
		end

		if frame.Parent then
			frame:Destroy()
		end
	end

	table.insert(Lib._watermarks, api)
	return api
end

function Lib:Watermark(config)
	return self:CreateWatermark(config)
end

function Lib:termark(config)
	return self:CreateWatermark(config)
end

function Lib:DestroyAll()
	local windows = self.Windows
	self.Windows = {}
	for index = #windows, 1, -1 do
		local window = windows[index]
		if window and type(window.Destroy) == "function" then
			pcall(function()
				window:Destroy()
			end)
		end
	end
	local watermarks = self._watermarks
	self._watermarks = {}
	for index = #watermarks, 1, -1 do
		local watermark = watermarks[index]
		if watermark and type(watermark.Destroy) == "function" then
			pcall(function()
				watermark:Destroy()
			end)
		end
	end
	if self._notifyGui then
		pcall(function()
			self._notifyGui:Destroy()
		end)
	end
	self._notifyGui = nil
	self._notifyStack = nil
	if self._watermarkGui then
		pcall(function()
			self._watermarkGui:Destroy()
		end)
	end
	self._watermarkGui = nil
	local sharedTable = getShared(false)
	if sharedTable then
		sharedTable.MikuLibInstance = nil
	end
end

function Lib:CloseAll()
	self:DestroyAll()
end

function Lib:Destroy()
	self:DestroyAll()
end

function Lib.new(a, b)
	if a == Lib then
		return Lib:CreateWindow(b)
	end
	return Lib:CreateWindow(a)
end

return Lib
