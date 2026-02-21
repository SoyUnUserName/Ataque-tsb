local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local lp = Players.LocalPlayer
local mouse = lp:GetMouse()

local targetChar = nil
local loopConnection = nil
local enabled = false
local currentMode = "Default"
local guiVisible = true

local collisionStates = {}

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = lp:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999999
ScreenGui.IgnoreGuiInset = true
ScreenGui.Enabled = true

local Frame = Instance.new("Frame")
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.BorderSizePixel = 0
Frame.Position = UDim2.new(0.5, -130, 0.5, -90)
Frame.Size = UDim2.new(0, 260, 0, 180)
Frame.Active = true
Frame.Draggable = false

local FrameCorner = Instance.new("UICorner")
FrameCorner.CornerRadius = UDim.new(0, 12)
FrameCorner.Parent = Frame

local FrameStroke = Instance.new("UIStroke")
FrameStroke.Color = Color3.fromRGB(0, 255, 0)
FrameStroke.Thickness = 1.5
FrameStroke.Parent = Frame

local TitleBar = Instance.new("Frame")
TitleBar.Parent = Frame
TitleBar.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
TitleBar.Size = UDim2.new(1, 0, 0, 38)

local TitleBarCorner = Instance.new("UICorner")
TitleBarCorner.CornerRadius = UDim.new(0, 12)
TitleBarCorner.Parent = TitleBar

local Title = Instance.new("TextLabel")
Title.Parent = TitleBar
Title.BackgroundTransparency = 1
Title.Size = UDim2.new(1, 0, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "143 H4CK3R"
Title.TextColor3 = Color3.fromRGB(0, 255, 0)
Title.TextSize = 22

local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Parent = Frame
ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
ToggleBtn.BorderColor3 = Color3.fromRGB(0, 255, 0)
ToggleBtn.BorderSizePixel = 2
ToggleBtn.Position = UDim2.new(0.1, 0, 0.27, 0)
ToggleBtn.Size = UDim2.new(0.8, 0, 0.22, 0)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Text = "OFF"
ToggleBtn.TextColor3 = Color3.fromRGB(0, 255, 0)
ToggleBtn.TextSize = 26

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleBtn

local ModeBtn = Instance.new("TextButton")
ModeBtn.Parent = Frame
ModeBtn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
ModeBtn.BorderColor3 = Color3.fromRGB(0, 255, 0)
ModeBtn.BorderSizePixel = 2
ModeBtn.Position = UDim2.new(0.1, 0, 0.52, 0)
ModeBtn.Size = UDim2.new(0.8, 0, 0.20, 0)
ModeBtn.Font = Enum.Font.GothamBold
ModeBtn.Text = "Modo: Default"
ModeBtn.TextColor3 = Color3.fromRGB(0, 255, 0)
ModeBtn.TextSize = 20

local ModeCorner = Instance.new("UICorner")
ModeCorner.CornerRadius = UDim.new(0, 8)
ModeCorner.Parent = ModeBtn

local TargetLabel = Instance.new("TextLabel")
TargetLabel.Parent = Frame
TargetLabel.BackgroundTransparency = 1
TargetLabel.Position = UDim2.new(0.1, 0, 0.76, 0)
TargetLabel.Size = UDim2.new(0.8, 0, 0, 28)
TargetLabel.Font = Enum.Font.GothamBold
TargetLabel.Text = "NONE"
TargetLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
TargetLabel.TextSize = 17

local function updateToggle()
	ToggleBtn.Text = enabled and "ON" or "OFF"
end

local function updateModeButton()
	ModeBtn.Text = "Modo: " .. currentMode
end

local function getDisplayName(char)
	local plr = Players:GetPlayerFromCharacter(char)
	return plr and (plr.DisplayName or plr.Name) or char.Name
end

local function applyNoCollision()
	local char = lp.Character
	if not char then return end
	collisionStates = {}
	for _, part in ipairs(char:GetDescendants()) do
		if part:IsA("BasePart") then
			collisionStates[part] = part.CanCollide
			part.CanCollide = false
		end
	end
end

local function restoreCollision()
	for part, state in pairs(collisionStates) do
		if part and part.Parent then
			part.CanCollide = state
		end
	end
	collisionStates = {}
end

local function startLoop()
	if loopConnection then loopConnection:Disconnect() end
	
	loopConnection = RunService.Heartbeat:Connect(function()
		if not enabled or not targetChar or not targetChar.Parent then return end
		
		local myChar = lp.Character
		local targHRP = targetChar:FindFirstChild("HumanoidRootPart")
		local myHRP = myChar and myChar:FindFirstChild("HumanoidRootPart")
		
		if myHRP and targHRP then
			local targetPos = targHRP.Position
			local newPos
			
			if currentMode == "Default" then
				local distance = 4.05
				local behind = -targHRP.CFrame.LookVector * distance
				newPos = targetPos + behind
			elseif currentMode == "H4CK3R" then
				local radius = 3.75 + math.random() * 1.7
				local theta = math.random() * math.pi * 2
				local phi = math.acos(2 * math.random() - 1)
				local x = radius * math.sin(phi) * math.cos(theta)
				local y = radius * math.sin(phi) * math.sin(theta)
				local z = radius * math.cos(phi)
				newPos = targetPos + Vector3.new(x, y, z)
			elseif currentMode == "43" then
				newPos = targetPos - Vector3.new(0, 4.05, 0)
			end
			
			myHRP.CFrame = CFrame.new(newPos, targetPos)
			myHRP.AssemblyLinearVelocity = Vector3.new(0,0,0)
			myHRP.AssemblyAngularVelocity = Vector3.new(0,0,0)
		end
	end)
end

local function stopLoop()
	if loopConnection then 
		loopConnection:Disconnect() 
		loopConnection = nil 
	end
end

local function toggleEnabled()
	enabled = not enabled
	updateToggle()
	
	if enabled then
		if (currentMode == "H4CK3R" or currentMode == "43") and lp.Character then
			applyNoCollision()
		end
	else
		restoreCollision()
		stopLoop()
		targetChar = nil
		TargetLabel.Text = "NONE"
	end
end

ToggleBtn.MouseButton1Click:Connect(toggleEnabled)

ModeBtn.MouseButton1Click:Connect(function()
	local modes = {"Default", "H4CK3R", "43"}
	for i, m in ipairs(modes) do
		if currentMode == m then
			currentMode = modes[i % #modes + 1]
			break
		end
	end
	updateModeButton()
	
	if enabled and lp.Character then
		if currentMode == "H4CK3R" or currentMode == "43" then
			applyNoCollision()
		else
			restoreCollision()
		end
		if targetChar then startLoop() end
	end
end)

mouse.Button1Down:Connect(function()
	if not enabled then return end
	local targetPart = mouse.Target
	if not targetPart then return end
	local char = targetPart
	while char and not char:FindFirstChild("Humanoid") do
		char = char.Parent
	end
	if char and char:FindFirstChild("HumanoidRootPart") and char ~= lp.Character then
		targetChar = char
		TargetLabel.Text = getDisplayName(char)
		
		if (currentMode == "H4CK3R" or currentMode == "43") and lp.Character then
			applyNoCollision()
		end
		startLoop()
	end
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.Z then
		guiVisible = not guiVisible
		ScreenGui.Enabled = guiVisible
	elseif input.KeyCode == Enum.KeyCode.C then
		toggleEnabled()
	end
end)

lp.CharacterAdded:Connect(function(char)
	if enabled and (currentMode == "H4CK3R" or currentMode == "43") then
		char:WaitForChild("HumanoidRootPart")
		applyNoCollision()
	end
end)

updateToggle()
updateModeButton()
-- H4CK3R = 43
