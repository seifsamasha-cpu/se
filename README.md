--[[ 
    SH HUB - THE ULTIMATE SCRIPT
    الميزات المدمجة:
    1. زر SH الجانبي (قابل للسحب).
    2. رادار "أغلى لاعب" في المنطقة الزرقاء أعلى منتصف الشاشة.
    3. Timer ESP يظهر "فوق" يافطات البيوت مباشرة (Unlocked/Timer).
    4. سرعة خارقة (Speed 100) ثابتة.
    5. قفز لانهائي (Infinite Jump) - تنط كل ما تدوس مسافة.
]]

local player = game.Players.LocalPlayer
local userInputService = game:GetService("UserInputService")
local runService = game:GetService("RunService")

-- إنشاء الواجهة الأساسية
local screenGui = Instance.new("ScreenGui", player.PlayerGui)
screenGui.Name = "SH_Ultimate_Final"
screenGui.ResetOnSpawn = false

-- 1. رادار أعلى قيمة (أعلى منتصف الشاشة)
local topBar = Instance.new("TextLabel", screenGui)
topBar.Size = UDim2.new(0, 500, 0, 40)
topBar.Position = UDim2.new(0.5, -250, 0, 15)
topBar.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
topBar.BackgroundTransparency = 0.5
topBar.Text = "SH SCANNER: LOADING DATA..."
topBar.TextColor3 = Color3.fromRGB(255, 50, 50)
topBar.Font = Enum.Font.GothamBold
topBar.TextSize = 20
local barCorner = Instance.new("UICorner", topBar)

-- 2. زر SH الجانبي
local sideButton = Instance.new("TextButton", screenGui)
sideButton.Size = UDim2.new(0, 50, 0, 50)
sideButton.Position = UDim2.new(0, 15, 0.4, 0)
sideButton.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
sideButton.Text = "SH"
sideButton.TextColor3 = Color3.new(1, 1, 1)
sideButton.Font = Enum.Font.GothamBold
sideButton.TextSize = 20
Instance.new("UICorner", sideButton)

-- [[ 3. ميزة السرعة والقفز اللانهائي ]]
local walkSpeedValue = 100

runService.RenderStepped:Connect(function()
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.WalkSpeed = walkSpeedValue
    end
end)

userInputService.JumpRequest:Connect(function()
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- [[ 4. ميزة ESP الوقت فوق اليافطات ]]
local function createSignESP(house)
    -- البحث عن اليافطة بناءً على الصور (Sign أو Head)
    local sign = house:FindFirstChild("Sign") or house:FindFirstChild("Head")
    if sign and not sign:FindFirstChild("SHTimer") then
        local billboard = Instance.new("BillboardGui", sign)
        billboard.Name = "SHTimer"
        billboard.Size = UDim2.new(0, 120, 0, 45)
        billboard.AlwaysOnTop = true
        billboard.ExtentsOffset = Vector3.new(0, 3.5, 0) -- لظهور النص "فوق" اليافطة بمسافة
        
        local label = Instance.new("TextLabel", billboard)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.Font = Enum.Font.GothamBold
        label.TextSize = 18
        label.TextStrokeTransparency = 0

        spawn(function()
            while billboard.Parent do
                -- جلب الوقت من موديل البيت
                local timer = house:FindFirstChild("Timer") or house:GetAttribute("Time")
                if timer and tonumber(timer) and tonumber(timer) > 0 then
                    label.Text = "⏳ " .. tostring(timer) .. "s"
                    label.TextColor3 = Color3.new(1, 1, 0) -- أصفر
                else
                    label.Text = "🔓 UNLOCKED"
                    label.TextColor3 = Color3.new(0, 1, 0) -- أخضر
                end
                wait(1)
            end
        end)
    end
end

-- [[ 5. تحديث الرادار العلوي ومسح الماب ]]
spawn(function()
    while true do
        local maxVal = 0
        local topName = "None"
        
        -- البحث عن اللاعب الأغنى
        for _, p in pairs(game.Players:GetPlayers()) do
            local stats = p:FindFirstChild("leaderstats")
            if stats then
                local money = stats:FindFirstChild("Money") or stats:FindFirstChild("Value")
                if money and money.Value > maxVal then
                    maxVal = money.Value
                    topName = p.Name
                end
            end
        end
        topBar.Text = "⭐ HIGHEST: " .. topName .. " [" .. tostring(maxVal) .. "]"
        
        -- تفعيل الـ ESP لكل البيوت
        for _, obj in pairs(workspace:GetChildren()) do
            if obj:FindFirstChild("Sign") or obj:FindFirstChild("Head") then
                createSignESP(obj)
            end
        end
        wait(5)
    end
end)

-- نظام سحب الزر SH
local function makeDraggable(obj)
    local dragging, dragInput, dragStart, startPos
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true; dragStart = input.Position; startPos = obj.Position
        end
    end)
    userInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    userInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
end
makeDraggable(sideButton)

print("SH HUB LOADED: ALL FEATURES ACTIVE!")
