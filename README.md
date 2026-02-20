--[[
    S.sHub - Theft Map Script (Blue Edition)
    الوظيفة: التحكم في السرعة وقوة القفز مع واجهة زرقاء احترافية
]]

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local frame = script.Parent

-- إعدادات الألوان والشكل للواجهة (S.sHub Style)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 40) -- أزرق غامق للخلفية
frame.BorderSizePixel = 2
frame.BorderColor3 = Color3.fromRGB(0, 170, 255) -- إطار أزرق فاتح

-- ربط العناصر
local speedInput = frame:WaitForChild("SpeedBox")
local speedButton = frame:WaitForChild("SpeedBtn")
local jumpInput = frame:WaitForChild("JumpBox")
local jumpButton = frame:WaitForChild("JumpBtn")

-- تلوين الزراير والـ Inputs تلقائياً
local function styleElement(el, isButton)
	if isButton then
		el.BackgroundColor3 = Color3.fromRGB(0, 120, 215) -- أزرق زاهي للزراير
		el.TextColor3 = Color3.new(1, 1, 1) -- نص أبيض
	else
		el.BackgroundColor3 = Color3.fromRGB(30, 30, 60) -- أزرق كحلي للـ Textbox
		el.TextColor3 = Color3.fromRGB(0, 255, 255) -- نص بلون فسفوري (Cyan)
	end
end

styleElement(speedButton, true)
styleElement(jumpButton, true)
styleElement(speedInput, false)
styleElement(jumpInput, false)

-- وظيفة تغيير السرعة (Speed)
speedButton.MouseButton1Click:Connect(function()
	local amount = tonumber(speedInput.Text)
	if amount then
		humanoid.WalkSpeed = amount
		print("S.sHub: Speed set to " .. amount)
	else
		speedInput.PlaceholderText = "اكتب رقم!"
		speedInput.Text = ""
	end
end)

-- وظيفة تغيير القفز (Jump)
jumpButton.MouseButton1Click:Connect(function)
	local amount = tonumber(jumpInput.Text)
	if amount then
		humanoid.UseJumpPower = true 
		humanoid.JumpPower = amount
		print("S.sHub: Jump set to " .. amount)
	else
		jumpInput.PlaceholderText = "اكتب رقم!"
		jumpInput.Text = ""
	end
end)
