local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Player = Players.LocalPlayer
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local RevealButton = Instance.new("TextButton")
local HideButton = Instance.new("TextButton")

ScreenGui.Name = "CoinCollectorUI"
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
MainFrame.Size = UDim2.new(0, 200, 0, 100)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

RevealButton.Name = "RevealButton"
RevealButton.Parent = MainFrame
RevealButton.BackgroundColor3 = Color3.fromRGB(0, 85, 255)
RevealButton.Size = UDim2.new(0, 180, 0, 40)
RevealButton.Position = UDim2.new(0, 10, 0, 10)
RevealButton.Font = Enum.Font.SourceSans
RevealButton.Text = "HitBox"
RevealButton.TextColor3 = Color3.fromRGB(255, 255, 255)
RevealButton.TextSize = 24

HideButton.Name = "HideButton"
HideButton.Parent = MainFrame
HideButton.BackgroundColor3 = Color3.fromRGB(0, 255, 127)
HideButton.Size = UDim2.new(0, 180, 0, 40)
HideButton.Position = UDim2.new(0, 10, 0, 60)
HideButton.Font = Enum.Font.SourceSans
HideButton.Text = "Stop"
HideButton.TextColor3 = Color3.fromRGB(255, 255, 255)
HideButton.TextSize = 24

local HeadSize = 35
local IsEffectActive = false 
local IsTeamCheckEnabled = false 
local effectConnection
local originalParts = {}

local function ApplyEffect()
	if not IsEffectActive then return end
	local localPlayer = Players.LocalPlayer
	if not localPlayer then return end

	local localPlayerTeam = localPlayer.Team

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= localPlayer and (not IsTeamCheckEnabled or player.Team ~= localPlayerTeam) then
			local humanoidRootPart = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
			if humanoidRootPart then
				if not originalParts[player] then
					originalParts[player] = {
						Size = humanoidRootPart.Size,
						Transparency = humanoidRootPart.Transparency,
						BrickColor = humanoidRootPart.BrickColor,
						Material = humanoidRootPart.Material,
						CanCollide = humanoidRootPart.CanCollide
					}
				end

				humanoidRootPart.Size = Vector3.new(HeadSize, HeadSize, HeadSize)
				humanoidRootPart.Transparency = 0.4
				humanoidRootPart.BrickColor = BrickColor.new("Really blue")
				humanoidRootPart.Material = Enum.Material.Neon
				humanoidRootPart.CanCollide = false
			end
		end
	end
end

local function ResetEffect()
	for _, player in ipairs(Players:GetPlayers()) do
		local humanoidRootPart = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
		if humanoidRootPart then
			if originalParts[player] then
				local original = originalParts[player]
				humanoidRootPart.Size = original.Size
				humanoidRootPart.Transparency = original.Transparency
				humanoidRootPart.BrickColor = original.BrickColor
				humanoidRootPart.Material = original.Material
				humanoidRootPart.CanCollide = original.CanCollide
			end
		end
	end
end

local function EnableEffect()
	if IsEffectActive then return end
	IsEffectActive = true
	effectConnection = RunService.RenderStepped:Connect(ApplyEffect)
end

local function DisableEffect()
	if not IsEffectActive then return end
	IsEffectActive = false
	if effectConnection then
		effectConnection:Disconnect()
		effectConnection = nil
	end
	ResetEffect()

	local localPlayer = Players.LocalPlayer
	local humanoidRootPart = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
	if humanoidRootPart then
		if originalParts[localPlayer] then
			local original = originalParts[localPlayer]
			humanoidRootPart.Size = original.Size
			humanoidRootPart.Transparency = original.Transparency
			humanooidRootPart.BrickColor = original.BrickColor
			humanoidRootPart.Material = original.Material
			humanoidRootPart.CanCollide = original.CanCollide
		end
	end
end

RevealButton.MouseButton1Click:Connect(function()
	EnableEffect()
end)

HideButton.MouseButton1Click:Connect(function()
	DisableEffect()
end)
