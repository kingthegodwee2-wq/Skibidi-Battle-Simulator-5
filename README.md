-- Skibidi Battle Simulator - Simple Purple +1 Gem GUI

local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GemGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Draggable Purple T Button (Open / Close)
local tButton = Instance.new("TextButton")
tButton.Size = UDim2.new(0, 55, 0, 55)
tButton.Position = UDim2.new(0.5, -27, 0.3, -80)
tButton.BackgroundColor3 = Color3.fromRGB(140, 50, 220)
tButton.Text = "T"
tButton.TextColor3 = Color3.new(1,1,1)
tButton.TextScaled = true
tButton.Font = Enum.Font.GothamBold
tButton.Parent = screenGui

local tCorner = Instance.new("UICorner")
tCorner.CornerRadius = UDim.new(1, 0)
tCorner.Parent = tButton

-- Draggable T Button
local draggingT = false
tButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        draggingT = true
        local dragStart = input.Position
        local startPos = tButton.Position
        local conn = game:GetService("UserInputService").InputChanged:Connect(function(inp)
            if draggingT and (inp.UserInputType == Enum.UserInputType.MouseMovement or inp.UserInputType == Enum.UserInputType.Touch) then
                local delta = inp.Position - dragStart
                tButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            end
        end)
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then draggingT = false conn:Disconnect() end
        end)
    end
end)

-- Main Frame
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 240, 0, 170)
frame.Position = UDim2.new(0.5, -120, 0.5, -85)
frame.BackgroundColor3 = Color3.fromRGB(30, 25, 50)
frame.BorderSizePixel = 0
frame.Visible = false
frame.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 12)
uiCorner.Parent = frame

-- Top Bar (No Exit Button)
local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, 0, 0, 45)
topBar.BackgroundColor3 = Color3.fromRGB(45, 35, 70)
topBar.Parent = frame

local profileIcon = Instance.new("ImageLabel")
profileIcon.Size = UDim2.new(0, 32, 0, 32)
profileIcon.Position = UDim2.new(0, 12, 0.5, -16)
profileIcon.BackgroundTransparency = 1
profileIcon.Image = "rbxthumb://type=AvatarHeadShot&id=" .. player.UserId .. "&w=48&h=48"
profileIcon.Parent = topBar

local profileName = Instance.new("TextLabel")
profileName.Size = UDim2.new(1, -60, 1, 0)
profileName.Position = UDim2.new(0, 55, 0, 0)
profileName.BackgroundTransparency = 1
profileName.Text = player.DisplayName .. "'s Gems"
profileName.TextColor3 = Color3.new(1,1,1)
profileName.TextScaled = true
profileName.Font = Enum.Font.GothamBold
profileName.Parent = topBar

-- Purple +1 Gem Button
local gemButton = Instance.new("TextButton")
gemButton.Size = UDim2.new(0.85, 0, 0, 68)
gemButton.Position = UDim2.new(0.075, 0, 0, 60)
gemButton.BackgroundColor3 = Color3.fromRGB(160, 60, 255)
gemButton.Text = "+1 Gem"
gemButton.TextColor3 = Color3.new(1,1,1)
gemButton.TextScaled = true
gemButton.Font = Enum.Font.GothamBold
gemButton.Parent = frame

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 12)
btnCorner.Parent = gemButton

-- Animations
local TweenService = game:GetService("TweenService")
local tweenInfo = TweenInfo.new(0.35, Enum.EasingStyle.Quint)

local function toggleGUI()
    if frame.Visible then
        local closeTween = TweenService:Create(frame, tweenInfo, {Size = UDim2.new(0,0,0,0)})
        closeTween:Play()
        closeTween.Completed:Connect(function()
            frame.Visible = false
        end)
    else
        frame.Visible = true
        frame.Size = UDim2.new(0,0,0,0)
        TweenService:Create(frame, tweenInfo, {Size = UDim2.new(0,240,0,170)}):Play()
    end
end

-- Draggable Main Window
local dragging
topBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        local dragStart = input.Position
        local startPos = frame.Position
        local conn = game:GetService("UserInputService").InputChanged:Connect(function(inp)
            if dragging and (inp.UserInputType == Enum.UserInputType.MouseMovement or inp.UserInputType == Enum.UserInputType.Touch) then
                local delta = inp.Position - dragStart
                frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            end
        end)
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then dragging = false conn:Disconnect() end
        end)
    end
end)

-- Button Functions
gemButton.MouseButton1Click:Connect(function()
    pcall(function()
        firesignal(game:GetService("ReplicatedStorage").Events.GameEvent.OnClientEvent, {
            type = "NotifyPlayer",
            method = "N_RefreshGemUI",
            args = {1, 1, Vector3.new(0,0,0)}
        })
    end)
end)

tButton.MouseButton1Click:Connect(toggleGUI)

-- Initial open
frame.Visible = true
openGUI = function() 
    frame.Visible = true
    frame.Size = UDim2.new(0,0,0,0)
    TweenService:Create(frame, tweenInfo, {Size = UDim2.new(0,240,0,170)}):Play()
end
openGUI()

print("✅ Smaller Purple GUI Loaded! Tap T to open/close.")
