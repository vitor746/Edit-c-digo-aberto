
local LocalPlayer = game:GetService("Players").LocalPlayer
local position = UDim2.new(0.13, 0, 0.22, 0)
local character
local humanoid
local rootPart
local isActive = false
local useNetworkLag = false
local phonkEnabled = true

local recentImages = {}
local connections = {
    jumpState = nil;
    jumpEvent = nil;
    died = nil;
}

local imageIds = {
    111668097052966, 70779085177724, 77738443752337, 140141817299964, 
    70851652799002, 6862780932, 88280205616409, 95217723213760, 
    123379963156438, 70454177817502, 70808925800394, 101553239615549, 
    90343700047338, 111025726278377, 91065224962965, 79098125487093, 
    71310918846614, 91195350749003, 80691431290501, 101859385855395
}

local soundIds = {
    132660906496320, 107010788063483, 103314799625839, 117449944483880, 
    104502600651938, 118882336251835, 128728161096602, 75825707123472, 
    105577499785211, 119173817873470, 104118543761603, 127128952417480, 
    135288379712506, 109590941994974, 101727279400780, 117349561398713, 
    85572465791029, 119205525477797, 122540560556310, 91210683361426, 
    95326356257817, 98883929964567, 134838527656195, 140133127946276, 
    109492585324475, 110350050930782, 106863674222879, 137789851538286, 
    84739018646963, 136173783192858, 120671634759596, 94313971179713, 
    115518117017358, 118487864701384, 140180426961277, 78812198128578, 
    131970292707600, 81114028045826, 98589713409772, 120381135704348, 
    135976814082473
}

local effectDuration = 1
local PhonkDuration = LocalPlayer:GetAttribute("PhonkDuration")
if type(PhonkDuration) == "number" then
    if PhonkDuration < 1 then
        effectDuration = 1
    elseif 3 < PhonkDuration then
        effectDuration = 3
    else
        effectDuration = PhonkDuration
    end
else
    LocalPlayer:SetAttribute("PhonkDuration", effectDuration)
end

local ColorCorrectionEffect = Instance.new("ColorCorrectionEffect")
ColorCorrectionEffect.Parent = game:GetService("Lighting")
ColorCorrectionEffect.Enabled = false
ColorCorrectionEffect.Saturation = -1
ColorCorrectionEffect.Contrast = 0.05

local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
for _, v in ipairs(PlayerGui:GetChildren()) do
    if v.Name == "PhonkLocalGUI" then
        v:Destroy()
    end
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PhonkLocalGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

local ImageLabel = Instance.new("ImageLabel")
ImageLabel.Name = "PhonkImage"
ImageLabel.AnchorPoint = Vector2.new(0.5, 0.5)
ImageLabel.Position = UDim2.new(0.5, 0, 0.78, 0)
ImageLabel.Size = position
ImageLabel.BackgroundTransparency = 1
ImageLabel.Visible = false
ImageLabel.Parent = ScreenGui

local SettingsButton = Instance.new("TextButton")
SettingsButton.Name = "PhonkGear"
SettingsButton.AnchorPoint = Vector2.new(1, 0)
SettingsButton.Position = UDim2.new(1, -18, 0, 12)
SettingsButton.Size = UDim2.new(0, 72, 0, 54)
SettingsButton.BackgroundTransparency = 0.12
SettingsButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
SettingsButton.BorderSizePixel = 0
SettingsButton.Text = "⚙"
SettingsButton.Font = Enum.Font.Gotham
SettingsButton.TextSize = 28
SettingsButton.TextColor3 = Color3.fromRGB(240, 240, 240)
SettingsButton.Parent = ScreenGui
Instance.new("UICorner", SettingsButton).CornerRadius = UDim.new(0, 8)

local SettingsFrame = Instance.new("Frame")
SettingsFrame.Name = "PhonkSettings"
SettingsFrame.AnchorPoint = Vector2.new(1, 0)
SettingsFrame.Position = UDim2.new(1, -18, 0, 88)
SettingsFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
SettingsFrame.Size = UDim2.new(0, 360, 0, 0)
SettingsFrame.AutomaticSize = Enum.AutomaticSize.Y
SettingsFrame.Visible = false
SettingsFrame.BorderSizePixel = 0
SettingsFrame.Parent = ScreenGui
Instance.new("UICorner", SettingsFrame).CornerRadius = UDim.new(0, 10)

local UIStroke = Instance.new("UIStroke", SettingsFrame)
UIStroke.Color = Color3.fromRGB(60, 60, 60)
UIStroke.Transparency = 0.6
UIStroke.Thickness = 1

local UIPadding = Instance.new("UIPadding", SettingsFrame)
UIPadding.PaddingTop = UDim.new(0, 10)
UIPadding.PaddingLeft = UDim.new(0, 10)
UIPadding.PaddingRight = UDim.new(0, 10)
UIPadding.PaddingBottom = UDim.new(0, 10)

local UIListLayout = Instance.new("UIListLayout", SettingsFrame)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 8)

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Name = "Title"
TitleLabel.Size = UDim2.new(1, 0, 0, 24)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextSize = 16
TitleLabel.TextColor3 = Color3.fromRGB(230, 230, 230)
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Text = "Phonk Settings"
TitleLabel.Parent = SettingsFrame

local PhonkToggleFrame = Instance.new("Frame")
PhonkToggleFrame.Name = "PhonkToggle"
PhonkToggleFrame.Size = UDim2.new(1, 0, 0, 32)
PhonkToggleFrame.BackgroundTransparency = 1
PhonkToggleFrame.Parent = SettingsFrame

local PhonkToggleLayout = Instance.new("UIListLayout", PhonkToggleFrame)
PhonkToggleLayout.FillDirection = Enum.FillDirection.Horizontal
PhonkToggleLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
PhonkToggleLayout.VerticalAlignment = Enum.VerticalAlignment.Center
PhonkToggleLayout.Padding = UDim.new(0, 10)

local PhonkToggleButton = Instance.new("TextButton")
PhonkToggleButton.Name = "PhonkToggleButton"
PhonkToggleButton.Size = UDim2.new(0, 20, 0, 20)
PhonkToggleButton.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
PhonkToggleButton.Text = ""
PhonkToggleButton.Parent = PhonkToggleFrame
Instance.new("UICorner", PhonkToggleButton).CornerRadius = UDim.new(0, 4)

local PhonkToggleLabel = Instance.new("TextLabel")
PhonkToggleLabel.Name = "PhonkToggleLabel"
PhonkToggleLabel.Size = UDim2.new(1, -30, 0, 20)
PhonkToggleLabel.BackgroundTransparency = 1
PhonkToggleLabel.Font = Enum.Font.Gotham
PhonkToggleLabel.TextSize = 14
PhonkToggleLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
PhonkToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
PhonkToggleLabel.Text = "Enable Phonk Effects"
PhonkToggleLabel.Parent = PhonkToggleFrame

local LagToggleFrame = Instance.new("Frame")
LagToggleFrame.Name = "LagToggle"
LagToggleFrame.Size = UDim2.new(1, 0, 0, 32)
LagToggleFrame.BackgroundTransparency = 1
LagToggleFrame.Parent = SettingsFrame

local LagToggleLayout = Instance.new("UIListLayout", LagToggleFrame)
LagToggleLayout.FillDirection = Enum.FillDirection.Horizontal
LagToggleLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
LagToggleLayout.VerticalAlignment = Enum.VerticalAlignment.Center
LagToggleLayout.Padding = UDim.new(0, 10)

local LagToggleButton = Instance.new("TextButton")
LagToggleButton.Name = "LagToggleButton"
LagToggleButton.Size = UDim2.new(0, 20, 0, 20)
LagToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
LagToggleButton.Text = ""
LagToggleButton.Parent = LagToggleFrame
Instance.new("UICorner", LagToggleButton).CornerRadius = UDim.new(0, 4)

local LagToggleLabel = Instance.new("TextLabel")
LagToggleLabel.Name = "LagToggleLabel"
LagToggleLabel.Size = UDim2.new(1, -30, 0, 20)
LagToggleLabel.BackgroundTransparency = 1
LagToggleLabel.Font = Enum.Font.Gotham
LagToggleLabel.TextSize = 14
LagToggleLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
LagToggleLabel.TextXAlignment = Enum.TextXAlignment.Left
LagToggleLabel.Text = "Enable Network Lag (Experimental)"
LagToggleLabel.Parent = LagToggleFrame

local CurrentLabel = Instance.new("TextLabel")
CurrentLabel.Name = "CurrentLabel"
CurrentLabel.Size = UDim2.new(1, 0, 0, 20)
CurrentLabel.BackgroundTransparency = 1
CurrentLabel.Font = Enum.Font.Gotham
CurrentLabel.TextSize = 14
CurrentLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
CurrentLabel.TextXAlignment = Enum.TextXAlignment.Left
CurrentLabel.Text = "Effect time: "..string.format("%.2f", effectDuration)..'s'
CurrentLabel.Parent = SettingsFrame

local ControlRow = Instance.new("Frame")
ControlRow.Name = "ControlRow"
ControlRow.Size = UDim2.new(1, 0, 0, 40)
ControlRow.BackgroundTransparency = 1
ControlRow.Parent = SettingsFrame

local ControlLayout = Instance.new("UIListLayout", ControlRow)
ControlLayout.FillDirection = Enum.FillDirection.Horizontal
ControlLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
ControlLayout.VerticalAlignment = Enum.VerticalAlignment.Center
ControlLayout.Padding = UDim.new(0, 12)

local MinusButton = Instance.new("TextButton")
MinusButton.Name = "Minus"
MinusButton.Size = UDim2.new(0, 44, 0, 36)
MinusButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MinusButton.Font = Enum.Font.Gotham
MinusButton.Text = '-'
MinusButton.TextSize = 22
MinusButton.TextColor3 = Color3.fromRGB(240, 240, 240)
MinusButton.Parent = ControlRow
Instance.new("UICorner", MinusButton)

local ValueLabel = Instance.new("TextLabel")
ValueLabel.Name = "Value"
ValueLabel.Size = UDim2.new(0, 160, 0, 36)
ValueLabel.BackgroundTransparency = 1
ValueLabel.Font = Enum.Font.GothamBold
ValueLabel.TextSize = 16
ValueLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
ValueLabel.Text = string.format("%.2f s", effectDuration)
ValueLabel.Parent = ControlRow

local PlusButton = Instance.new("TextButton")
PlusButton.Name = "Plus"
PlusButton.Size = UDim2.new(0, 44, 0, 36)
PlusButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
PlusButton.Font = Enum.Font.Gotham
PlusButton.Text = '+'
PlusButton.TextSize = 22
PlusButton.TextColor3 = Color3.fromRGB(240, 240, 240)
PlusButton.Parent = ControlRow
Instance.new("UICorner", PlusButton)

local ActionsFrame = Instance.new("Frame")
ActionsFrame.Name = "Actions"
ActionsFrame.Size = UDim2.new(1, 0, 0, 36)
ActionsFrame.BackgroundTransparency = 1
ActionsFrame.Parent = SettingsFrame

local ActionsLayout = Instance.new("UIListLayout", ActionsFrame)
ActionsLayout.FillDirection = Enum.FillDirection.Horizontal
ActionsLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
ActionsLayout.VerticalAlignment = Enum.VerticalAlignment.Center
ActionsLayout.Padding = UDim.new(0, 12)

local ResetButton = Instance.new("TextButton")
ResetButton.Name = "Reset"
ResetButton.Size = UDim2.new(0, 140, 0, 36)
ResetButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
ResetButton.Font = Enum.Font.Gotham
ResetButton.Text = "Reset"
ResetButton.TextSize = 14
ResetButton.TextColor3 = Color3.fromRGB(230, 230, 230)
ResetButton.Parent = ActionsFrame
Instance.new("UICorner", ResetButton)

local CloseButton = Instance.new("TextButton")
CloseButton.Name = "Close"
CloseButton.Size = UDim2.new(0, 140, 0, 36)
CloseButton.BackgroundColor3 = Color3.fromRGB(90, 40, 40)
CloseButton.Font = Enum.Font.Gotham
CloseButton.Text = "Close"
CloseButton.TextSize = 14
CloseButton.TextColor3 = Color3.fromRGB(240, 240, 240)
CloseButton.Parent = ActionsFrame
Instance.new("UICorner", CloseButton)

local function updateToggles()
    if phonkEnabled then
        PhonkToggleButton.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
    else
        PhonkToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    end
    
    if useNetworkLag and phonkEnabled then
        LagToggleButton.BackgroundColor3 = Color3.fromRGB(40, 120, 60)
    else
        LagToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        useNetworkLag = false
    end
end

local function setNetworkLag(value)
    pcall(function()
        if value then
            settings().Network.IncomingReplicationLag = math.huge
        else
            settings().Network.IncomingReplicationLag = 0
        end
    end)
end

local function cleanupLag()
    setNetworkLag(false)
end

local function pickNonRecent(items, recentList)
    if #items == 0 then
        return nil
    end
    
    local available = {}
    for _, item in ipairs(items) do
        local found = false
        for _, recent in ipairs(recentList) do
            if recent == item then
                found = true
                break
            end
        end
        if not found then
            table.insert(available, item)
        end
    end
    
    if #available == 0 then
        return items[math.random(1, #items)]
    end
    
    local selected = available[math.random(1, #available)]
    table.insert(recentList, selected)
    if #recentList > 3 then
        table.remove(recentList, 1)
    end
    return selected
end

local function getRandomImage()
    if #imageIds == 0 then
        return nil
    end
    
    local selectedId = pickNonRecent(imageIds, recentImages)
    if selectedId then
        return "rbxassetid://" .. selectedId
    end
    return nil
end

local function isSpeedCoilEquipped()
    if not character then
        return false
    end
    
    for _, item in ipairs(character:GetChildren()) do
        if item:IsA("Tool") and item.Name == "SpeedCoil" then
            return true
        end
    end
    return false
end

local function isBeingFlinged()
    if not rootPart or not rootPart.Parent then
        return false
    end
    
    local success, velocity = pcall(function()
        return rootPart.AssemblyLinearVelocity
    end)
    
    if not success or not velocity then
        return false
    end
    
    if isSpeedCoilEquipped() then
        if velocity.Magnitude >= 75 then
            return true
        end
        if math.abs(velocity.Y) >= 36 then
            return true
        end
        return false
    end
    
    if velocity.Magnitude >= 25 then
        return true
    end
    if math.abs(velocity.Y) >= 12 then
        return true
    end
    return false
end

local recentSounds = {}
local function playRandomSoundSegment(duration)
    if #soundIds == 0 then
        return
    end
    
    local soundId = pickNonRecent(soundIds, recentSounds)
    if not soundId then
        return
    end
    
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://" .. soundId
    sound.Volume = 3
    sound.Parent = workspace
    
    local function cleanup()
        if sound and sound.Parent then
            pcall(function()
                sound:Stop()
            end)
            pcall(function()
                sound:Destroy()
            end)
        end
    end
    
    local success = pcall(function()
        sound:Play()
        task.delay(duration, cleanup)
    end)
    
    if not success then
        cleanup()
    end
end

local TweenService = game:GetService("TweenService")
local function shakeImage(imageLabel)
    if not imageLabel then
        return
    end
    
    for i = 1, 16 do
        local tween = TweenService:Create(imageLabel, TweenInfo.new(0.03, Enum.EasingStyle.Linear), {
            Position = UDim2.new(0.5, math.random(-18, 18), 0.78, math.random(-12, 12))
        })
        tween:Play()
        tween.Completed:Wait()
    end
    
    TweenService:Create(imageLabel, TweenInfo.new(0.08, Enum.EasingStyle.Linear), {
        Position = UDim2.new(0.5, 0, 0.78, 0)
    }):Play()
end

local function doPhonkFreeze()
    if not phonkEnabled then
        return
    end
    
    if isActive then
        return
    end
    
    if isBeingFlinged() then
        return
    end
    
    isActive = true
    
    if useNetworkLag and phonkEnabled then
        setNetworkLag(true)
    end
    
    local imageUrl = getRandomImage()
    if imageUrl then
        ImageLabel.Image = imageUrl
        ImageLabel.Size = position
        ImageLabel.Position = UDim2.new(0.5, 0, 0.78, 0)
        ImageLabel.Visible = true
        task.spawn(function()
            shakeImage(ImageLabel)
        end)
    else
        ImageLabel.Visible = false
    end
    
    playRandomSoundSegment(math.max(effectDuration, 1))
    
    if rootPart and rootPart.Parent and not isBeingFlinged() then
        pcall(function()
            rootPart.Anchored = true
        end)
    end
    
    ColorCorrectionEffect.Enabled = true
    
    task.wait(effectDuration)
    
    if useNetworkLag and phonkEnabled then
        setNetworkLag(false)
    end
    
    if rootPart and rootPart.Parent then
        pcall(function()
            rootPart.Anchored = false
            rootPart.AssemblyLinearVelocity = Vector3.new(0, -25, 0)
        end)
        
        if humanoid then
            pcall(function()
                humanoid:ChangeState(Enum.HumanoidStateType.Freefall)
            end)
        end
    end
    
    ColorCorrectionEffect.Enabled = false
    ImageLabel.Visible = false
    isActive = false
end

local function disconnectConnections()
    cleanupLag()
    
    if connections.jumpState then
        pcall(function()
            connections.jumpState:Disconnect()
        end)
        connections.jumpState = nil
    end
    
    if connections.jumpEvent then
        pcall(function()
            connections.jumpEvent:Disconnect()
        end)
        connections.jumpEvent = nil
    end
    
    if connections.died then
        pcall(function()
            connections.died:Disconnect()
        end)
        connections.died = nil
    end
end

local deadHumans = {}
local function bindCharacter(newCharacter)
    disconnectConnections()
    
    character = newCharacter
    humanoid = character:WaitForChild("Humanoid")
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then
        root = character:WaitForChild("HumanoidRootPart")
    end
    rootPart = root
    
    deadHumans[humanoid] = nil
    
    connections.jumpState = humanoid.StateChanged:Connect(function(oldState, newState)
        if newState == Enum.HumanoidStateType.Jumping then
            if isActive or not phonkEnabled then
                return
            end
            
            task.delay(0.3, function()
                if humanoid and humanoid.Health > 0 and not isActive and not isBeingFlinged() and phonkEnabled then
                    doPhonkFreeze()
                end
            end)
        end
    end)
    
    if humanoid.Jumping then
        connections.jumpEvent = humanoid.Jumping:Connect(function(isJumping)
            if isJumping then
                if isActive or not phonkEnabled then
                    return
                end
                
                task.delay(0.3, function()
                    if humanoid and humanoid.Health > 0 and not isActive and not isBeingFlinged() and phonkEnabled then
                        doPhonkFreeze()
                    end
                end)
            end
        end)
    end
    
    connections.died = humanoid.Died:Connect(function()
        if deadHumans[humanoid] then
            return
        end
        
        deadHumans[humanoid] = true
        cleanupLag()
        
        if phonkEnabled then
            task.spawn(function()
                task.wait(0.03)
                if not isActive and not isBeingFlinged() then
                    doPhonkFreeze()
                end
            end)
        end
        
        task.delay(math.max(1, effectDuration + 0.1), function()
            if rootPart then
                pcall(function()
                    rootPart.Anchored = false
                end)
            end
            
            ColorCorrectionEffect.Enabled = false
            if ImageLabel then
                ImageLabel.Visible = false
            end
            
            isActive = false
            deadHumans[humanoid] = nil
        end)
    end)
end

if LocalPlayer.Character then
    bindCharacter(LocalPlayer.Character)
end

LocalPlayer.CharacterAdded:Connect(function(newCharacter)
    task.wait(0.1)
    bindCharacter(newCharacter)
end)

local lastChatTime = 0
LocalPlayer.Chatted:Connect(function(message)
    if not phonkEnabled then
        return
    end
    
    local currentTime = tick()
    if currentTime - lastChatTime < 0.25 then
        return
    end
    
    lastChatTime = currentTime
    if not isActive and not isBeingFlinged() then
        doPhonkFreeze()
    end
end)

local function updateUIAndSave()
    if effectDuration < 1 then
        effectDuration = 1
    elseif effectDuration > 3 then
        effectDuration = 3
    end
    
    LocalPlayer:SetAttribute("PhonkDuration", effectDuration)
    
    if ValueLabel then
        ValueLabel.Text = string.format("%.2f s", effectDuration)
    end
    
    if CurrentLabel then
        CurrentLabel.Text = "Effect time: "..string.format("%.2f", effectDuration)..'s'
    end
    
    updateToggles()
end

SettingsButton.MouseButton1Click:Connect(function()
    SettingsFrame.Visible = not SettingsFrame.Visible
end)

PhonkToggleButton.MouseButton1Click:Connect(function()
    phonkEnabled = not phonkEnabled
    if not phonkEnabled then
        cleanupLag()
        useNetworkLag = false
        ColorCorrectionEffect.Enabled = false
        ImageLabel.Visible = false
    end
    updateUIAndSave()
end)

LagToggleButton.MouseButton1Click:Connect(function()
    if not phonkEnabled then
        return
    end
    useNetworkLag = not useNetworkLag
    if not useNetworkLag then
        cleanupLag()
    end
    updateUIAndSave()
end)

MinusButton.MouseButton1Click:Connect(function()
    effectDuration = math.floor((effectDuration - 0.25) * 100 + 0.5) / 100
    updateUIAndSave()
end)

PlusButton.MouseButton1Click:Connect(function()
    effectDuration = math.floor((effectDuration + 0.25) * 100 + 0.5) / 100
    updateUIAndSave()
end)

ResetButton.MouseButton1Click:Connect(function()
    effectDuration = 1
    updateUIAndSave()
end)

CloseButton.MouseButton1Click:Connect(function()
    SettingsFrame.Visible = false
end)

LocalPlayer:GetAttributeChangedSignal("PhonkDuration"):Connect(function()
    local newDuration = LocalPlayer:GetAttribute("PhonkDuration")
    if type(newDuration) == "number" then
        if newDuration < 1 then
            effectDuration = 1
        elseif newDuration > 3 then
            effectDuration = 3
        else
            effectDuration = newDuration
        end
        updateUIAndSave()
    end
end)

game:GetService("Players").PlayerRemoving:Connect(function(player)
    if player == LocalPlayer then
        cleanupLag()
    end
end)

updateUIAndSave()
