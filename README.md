 local Settings = {
	
        Speed = 5,
        SprintSpeed = 30,
        ToggleKey = Enum.KeyCode.E,
        SprintKey = Enum.KeyCode.RightControl,
        
        ForwardKey = Enum.KeyCode.W,
        LeftKey = Enum.KeyCode.A,
        BackwardKey = Enum.KeyCode.S,
        RightKey = Enum.KeyCode.D,
        UpKey = Enum.KeyCode.E,
        DownKey = Enum.KeyCode.Q,
        
    }
    
    local Screen = Instance.new("ScreenGui",game.CoreGui)
    local Distance = Instance.new("TextLabel",Screen)
    Distance.BackgroundTransparency = 1
    Distance.Size = UDim2.new(0,10,0,10)
    Distance.ZIndex = 2
    Distance.Text = "0"
    Distance.TextStrokeTransparency = .5
    Distance.TextSize = 20
    Distance.TextStrokeColor3 = Color3.fromRGB(33, 33, 33)
    Distance.Font = Enum.Font.Gotham
    Distance.TextColor3 = Color3.new(1,1,1)
    Distance.TextXAlignment = Enum.TextXAlignment.Left
    Distance.TextYAlignment = Enum.TextYAlignment.Top
    
    
    local Mouse = game.Players.LocalPlayer:GetMouse()
    local Direction = Vector3.new(0,0,0)
    local InterpolatedDir = Direction
    local Tilt = 0
    local InterpolatedTilt = Tilt
    local RunService = game:GetService("RunService")
    local Toggled = false
    local Sprinting = false
    local CameraPos = game.Workspace.CurrentCamera.CFrame.Position
    
    pcall(function()
        game.Players.LocalPlayer.DevCameraOcclusionMode = Enum.DevCameraOcclusionMode.Invisicam	
    end)
    
    function Lerp(a, b, t)
        return a + (b - a) * t
    end
    
    local LastPos = nil
    
    function KeyHandler(actionName, userInputState)
        if true and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            if actionName == "Toggle" and userInputState == Enum.UserInputState.Begin then
                Toggled = not Toggled
                if Toggled then
                    LastPos = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
                    --game.Players.LocalPlayer.Character.HumanoidRootPart.Anchored = true
                    game.Players.LocalPlayer.Character.Humanoid.PlatformStand = true 
                else
                    LastPos = nil
                    game.Players.LocalPlayer.Character.Humanoid.PlatformStand = false
                    --game.Players.LocalPlayer.Character.HumanoidRootPart.Anchored = false
                end
            elseif actionName == "Forward" then
                Tilt = userInputState == Enum.UserInputState.Begin and -20 or 0
                Direction = Vector3.new(Direction.x,Direction.y,userInputState == Enum.UserInputState.Begin and -1 or 0)
            elseif actionName == "Left" then
                Direction = Vector3.new(userInputState == Enum.UserInputState.Begin and -1 or 0,Direction.y,Direction.z)
            elseif actionName == "Backward" then
                Tilt = userInputState == Enum.UserInputState.Begin and 20 or 0
                Direction = Vector3.new(Direction.x,Direction.y,userInputState == Enum.UserInputState.Begin and 1 or 0)
            elseif actionName == "Right" then
                Direction = Vector3.new(userInputState == Enum.UserInputState.Begin and 1 or 0,Direction.y,Direction.z)
            elseif actionName == "Up" then
                Direction = Vector3.new(Direction.x,userInputState == Enum.UserInputState.Begin and 1 or 0,Direction.z)
            elseif actionName == "Down" then
                Direction = Vector3.new(Direction.x,userInputState == Enum.UserInputState.Begin and -1 or 0,Direction.z)
            elseif actionName == "Sprint" then
                Sprinting = userInputState == Enum.UserInputState.Begin
            end
        end
    end
    
    
    
    game:GetService("UserInputService").InputBegan:connect(function(inputObject, gameProcessedEvent)
        
        if inputObject.KeyCode == Settings.ToggleKey then
            KeyHandler("Toggle", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.ForwardKey then
            KeyHandler("Forward", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.LeftKey then
            KeyHandler("Left", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.BackwardKey then
            KeyHandler("Backward", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.RightKey then
            KeyHandler("Right", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.UpKey then	
            KeyHandler("Up", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.DownKey then
            KeyHandler("Down", Enum.UserInputState.Begin, inputObject)
        elseif inputObject.KeyCode == Settings.SprintKey then
            KeyHandler("Sprint", Enum.UserInputState.Begin, inputObject)
        end
        
        
    end)
    
    
    game:GetService("UserInputService").InputEnded:connect(function(inputObject, gameProcessedEvent)
        
        if inputObject.KeyCode == Settings.ToggleKey then
            KeyHandler("Toggle", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.ForwardKey then
            KeyHandler("Forward", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.LeftKey then
            KeyHandler("Left", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.BackwardKey then
            KeyHandler("Backward", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.RightKey then
            KeyHandler("Right", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.UpKey then	
            KeyHandler("Up", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.DownKey then
            KeyHandler("Down", Enum.UserInputState.End, inputObject)
        elseif inputObject.KeyCode == Settings.SprintKey then
            KeyHandler("Sprint", Enum.UserInputState.End, inputObject)
        end
        
        
    end)
    
    
    RunService.RenderStepped:Connect(function()
        if Toggled and game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")  then
            for i,v in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.Velocity = Vector3.new(0,0,0)
                end
            end
            local RootPart = game.Players.LocalPlayer.Character.HumanoidRootPart
            if LastPos then
                Distance.Text = math.floor((LastPos-RootPart.Position).Magnitude+.5)
                if (LastPos-RootPart.Position).Magnitude >= 350 then
                    Distance.TextColor3 = Color3.new(1,0,0)
                else
                    Distance.TextColor3 = Color3.new(1,1,1)	
                end
            else
                Distance.TextColor3 = Color3.new(1,1,1)
                Distance.Text = 0
            end
            InterpolatedDir = InterpolatedDir:Lerp((Direction * (Sprinting and Settings.SprintSpeed or Settings.Speed)),.2)
            InterpolatedTilt = Lerp(InterpolatedTilt ,Tilt* (Sprinting and 2 or 1),Tilt == 0 and .2 or .1)
            RootPart.CFrame = RootPart.CFrame:Lerp(CFrame.new(RootPart.Position,RootPart.Position + Mouse.UnitRay.Direction) * CFrame.Angles(0,math.rad(00),0) * CFrame.new(InterpolatedDir)  * CFrame.Angles(math.rad(InterpolatedTilt),0,0),.2)
        else
            Distance.TextColor3 = Color3.new(1,1,1)
            Distance.Text = 0
        end	
    end)
     
end)

OtherPlayerSection:NewButton("[KINGLUNA] Noclip", "Noclip", function()
    local Workspace = game:GetService("Workspace")
local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local Noclip = Instance.new("ScreenGui")
local BG = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local Toggle = Instance.new("TextButton")
local StatusPF = Instance.new("TextLabel")
local Status = Instance.new("TextLabel")
local Credit = Instance.new("TextLabel")
local Plr = Players.LocalPlayer
local Clipon = false

Noclip.Name = "Noclip"
Noclip.Parent = game.CoreGui

BG.Name = "BG"
BG.Parent = Noclip
BG.BackgroundColor3 = Color3.new(0.0980392, 0.0980392, 0.0980392)
BG.BorderColor3 = Color3.new(0.0588235, 0.0588235, 0.0588235)
BG.BorderSizePixel = 2
BG.Position = UDim2.new(0.149479166, 0, 0.82087779, 0)
BG.Size = UDim2.new(0, 210, 0, 127)
BG.Active = true
BG.Draggable = true

Title.Name = "Title"
Title.Parent = BG
Title.BackgroundColor3 = Color3.new(0.266667, 0.00392157, 0.627451)
Title.BorderColor3 = Color3.new(0.180392, 0, 0.431373)
Title.BorderSizePixel = 2
Title.Size = UDim2.new(0, 210, 0, 33)
Title.Font = Enum.Font.Highway
Title.Text = "Noclip"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.FontSize = Enum.FontSize.Size32
Title.TextSize = 30
Title.TextStrokeColor3 = Color3.new(0.180392, 0, 0.431373)
Title.TextStrokeTransparency = 0

Toggle.Parent = BG
Toggle.BackgroundColor3 = Color3.new(0.266667, 0.00392157, 0.627451)
Toggle.BorderColor3 = Color3.new(0.180392, 0, 0.431373)
Toggle.BorderSizePixel = 2
Toggle.Position = UDim2.new(0.152380958, 0, 0.374192119, 0)
Toggle.Size = UDim2.new(0, 146, 0, 36)
Toggle.Font = Enum.Font.Highway
Toggle.FontSize = Enum.FontSize.Size28
Toggle.Text = "Toggle"
Toggle.TextColor3 = Color3.new(1, 1, 1)
Toggle.TextSize = 25
Toggle.TextStrokeColor3 = Color3.new(0.180392, 0, 0.431373)
Toggle.TextStrokeTransparency = 0

StatusPF.Name = "StatusPF"
StatusPF.Parent = BG
StatusPF.BackgroundColor3 = Color3.new(1, 1, 1)
StatusPF.BackgroundTransparency = 1
StatusPF.Position = UDim2.new(0.314285725, 0, 0.708661377, 0)
StatusPF.Size = UDim2.new(0, 56, 0, 20)
StatusPF.Font = Enum.Font.Highway
StatusPF.FontSize = Enum.FontSize.Size24
StatusPF.Text = "Status:"
StatusPF.TextColor3 = Color3.new(1, 1, 1)
StatusPF.TextSize = 20
StatusPF.TextStrokeColor3 = Color3.new(0.333333, 0.333333, 0.333333)
StatusPF.TextStrokeTransparency = 0
StatusPF.TextWrapped = true

Status.Name = "Status"
Status.Parent = BG
Status.BackgroundColor3 = Color3.new(1, 1, 1)
Status.BackgroundTransparency = 1
Status.Position = UDim2.new(0.580952346, 0, 0.708661377, 0)
Status.Size = UDim2.new(0, 56, 0, 20)
Status.Font = Enum.Font.Highway
Status.FontSize = Enum.FontSize.Size14
Status.Text = "off"
Status.TextColor3 = Color3.new(0.666667, 0, 0)
Status.TextScaled = true
Status.TextSize = 14
Status.TextStrokeColor3 = Color3.new(0.180392, 0, 0.431373)
Status.TextWrapped = true
Status.TextXAlignment = Enum.TextXAlignment.Left

Credit.Name = "Credit"
Credit.Parent = BG
Credit.BackgroundColor3 = Color3.new(1, 1, 1)
Credit.BackgroundTransparency = 1
Credit.Position = UDim2.new(0.195238099, 0, 0.866141737, 0)
Credit.Size = UDim2.new(0, 128, 0, 17)
Credit.Font = Enum.Font.SourceSans
Credit.FontSize = Enum.FontSize.Size18
Credit.Text = "Created by KingLuna"
Credit.TextColor3 = Color3.new(1, 1, 1)
Credit.TextSize = 16
Credit.TextStrokeColor3 = Color3.new(0.196078, 0.196078, 0.196078)
Credit.TextStrokeTransparency = 0
Credit.TextWrapped = true

Toggle.MouseButton1Click:connect(function()
	if Status.Text == "off" then
		Clipon = true
		Status.Text = "on"
		Status.TextColor3 = Color3.new(0,185,0)
		Stepped = game:GetService("RunService").Stepped:Connect(function()
			if not Clipon == false then
				for a, b in pairs(Workspace:GetChildren()) do
                if b.Name == Plr.Name then
                for i, v in pairs(Workspace[Plr.Name]:GetChildren()) do
                if v:IsA("BasePart") then
                v.CanCollide = false
                end end end end
			else
				Stepped:Disconnect()
			end
		end)
	elseif Status.Text == "on" then
		Clipon = false
		Status.Text = "off"
		Status.TextColor3 = Color3.new(170,0,0)
	end
end)
