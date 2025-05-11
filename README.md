--[[
Aimbot Arsenal
Created By ttk yt
]]

-- Variables
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local camera = workspace.CurrentCamera

-- Settings
local aimbotEnabled = false
local showMenu = true
local fovSize = 100
local teamCheck = true
local discordLink = "https://discord.gg/seulink"

-- GUI Setup
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "AimbotArsenalGUI"

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 250, 0, 300)
Frame.Position = UDim2.new(0.02, 0, 0.2, 0)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.BorderSizePixel = 0
Frame.Visible = true

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "Aimbot Arsenal"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 24

-- FOV Controls
local function createButton(text, posY, callback)
	local btn = Instance.new("TextButton", Frame)
	btn.Size = UDim2.new(1, -20, 0, 30)
	btn.Position = UDim2.new(0, 10, 0, posY)
	btn.Text = text
	btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.SourceSans
	btn.TextSize = 18
	btn.MouseButton1Click:Connect(callback)
	return btn
end

createButton("FOV +", 50, function() fovSize = fovSize + 10 end)
createButton("FOV -", 90, function() fovSize = math.max(10, fovSize - 10) end)

-- Team Check Button
createButton("Toggle Team Check", 130, function()
	teamCheck = not teamCheck
end)

-- Discord Button
createButton("Discord", 170, function()
	setclipboard(discordLink)
end)

-- Credit
local Credit = Instance.new("TextLabel", Frame)
Credit.Size = UDim2.new(1, 0, 0, 30)
Credit.Position = UDim2.new(0, 0, 1, -30)
Credit.BackgroundTransparency = 1
Credit.Text = "Created By ttk yt"
Credit.TextColor3 = Color3.new(1, 1, 1)
Credit.Font = Enum.Font.SourceSansItalic
Credit.TextSize = 16

-- Toggle Menu Visibility
UIS.InputBegan:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.P then
		showMenu = not showMenu
		Frame.Visible = showMenu
	end
end)

-- Draw FOV Circle
local circle = Drawing.new("Circle")
circle.Radius = fovSize
circle.Color = Color3.fromRGB(255, 255, 255)
circle.Filled = false
circle.Thickness = 1
circle.Visible = true

-- Get Closest Target
local function getClosestPlayer()
	local closest = nil
	local shortestDist = math.huge

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			if teamCheck and player.Team == LocalPlayer.Team then continue end

			local head = player.Character.Head
			local pos, onScreen = camera:WorldToViewportPoint(head.Position)

			if onScreen then
				local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
				if dist < fovSize and dist < shortestDist then
					shortestDist = dist
					closest = head
				end
			end
		end
	end

	return closest
end

-- Aimbot Main Loop
RunService.RenderStepped:Connect(function()
	circle.Position = Vector2.new(Mouse.X, Mouse.Y)
	circle.Radius = fovSize

	if UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
		local target = getClosestPlayer()
		if target then
			camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
		end
	end
end)
