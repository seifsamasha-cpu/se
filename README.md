--[[
    S.sHub - Theft Map Script (Blue Edition)
    التحديث: إضافة ميزة السحب (Dragging) وزرار الإخفاء (Toggle)
]]

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local userInputService = game:GetService("UserInputService")

local frame = script.Parent
local toggleKey = Enum.KeyCode.K -- الزرار اللي بيخفي ويظهر الواجهة (تقدر تغيره)

-- 1. ميزة تحريك الواجهة (Dragging System)
local dragging, dragInput, dragStart, startPos

local function update(input)
	local delta = input.Position - dragStart
	frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

frame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = frame.Position
		
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

frame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

userInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		update(input)
	end
end)

-- 2. ميزة الإخفاء والإظهار (Toggle UI)
userInputService.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == toggleKey then
		frame.Visible = not frame.Visible
		print("S.sHub: UI Visibility toggled.")
	end
end)

-- باقي الكود الخاص بالسرعة والقفز (نفس اللي فات مع تصحيح الـ Character)
player.CharacterAdded:Connect(function(newCharacter)
	character = newCharacter
	humanoid = newCharacter:WaitForChild("Humanoid")
end)

local speedInput = frame:WaitForChild("SpeedBox")
local speedButton = frame:WaitForChild("SpeedBtn")
local jumpInput = frame:WaitForChild("JumpBox")
local jumpButton = frame:WaitForChild("JumpBtn")

local function styleElement(el, isButton)
	if isButton then
		el.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
		el.TextColor3 = Color3.new(1, 1, 1)
	else
		el.BackgroundColor3 = Color3.fromRGB(30, 30, 60)
		el.TextColor3 = Color3.fromRGB(0, 255, 255)
	end
end

styleElement(speedButton, true)
styleElement(jumpButton, true)
styleElement(speedInput, false)
styleElement(jumpInput, false)

speedButton.MouseButton1Click:Connect(function()
	local amount = tonumber(speedInput.Text)
	if amount then
		humanoid.WalkSpeed = amount
	else
		speedInput.Text = ""; speedInput.PlaceholderText = "رقم!"
	end
end)

jumpButton.MouseButton1Click:Connect(function()
	local amount = tonumber(jumpInput.Text)
	if amount then
		humanoid.UseJumpPower = true 
		humanoid.JumpPower = amount
	else
		jumpInput.Text = ""; jumpInput.PlaceholderText = "رقم!"
	end
end)
