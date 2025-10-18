local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Ducky_HUB",
    LoadingTitle = "Ducky_HUB",
    LoadingSubtitle = "by luucasdy",
    ConfigurationSaving = {
       Enabled = true,
       FolderName = nil,
       FileName = "Big Hub"
    },
    Discord = {
       Enabled = true,
       Invite = "uFrpbrMf",
       RememberJoins = true
    },
    KeySystem = false,
    KeySettings = {
       Title = "DUCKY_HUB",
       Subtitle = "Key System",
       Note = "Use Ducky_HUB as key",
       FileName = "Key",
       SaveKey = Ducky_HUB,
       GrabKeyFromSite = Lootlabs,
       Key = {"Ducky_HUB"}
    }
})

local PlayerTab = Window:CreateTab("Discord", 4483362458)

local announce = PlayerTab:CreateLabel("Welcome to Ducky_HUB")
local announce = PlayerTab:CreateLabel("Join our discord for more scripts")
discord = PlayerTab:CreateButton({
    Name = "Copy Discord",
    Callback = function()
        setclipboard("https://discord.gg/uFrpbrMf")
    end,
})
local announce = PlayerTab:CreateLabel("Script made by luucasdy")
local announce = PlayerTab:CreateLabel("Enjoy the script!")
local announce = PlayerTab:CreateLabel("Ducky_HUB has been updated")
local announce = PlayerTab:CreateLabel("New features have been added")

local PlayerTab = Window:CreateTab("Player", 4483362458)
PlayerTab:CreateSlider({
    Name = "WalkSpeed",
    Range = {1, 10},
    Increment = 1,
    Suffix = "Speed",
    CurrentValue = 10,
    Flag = "Slider1",
    Callback = function(Value)
        game.Players.LocalPlayer.Character:SetAttribute("SpeedMultiplier", Value)
    end,
})
PlayerTab:CreateSlider({
    Name = "Dash length",
    Range = {10, 1000},
    Increment = 1,
    Suffix = "Length",
    CurrentValue = 10,
    Flag = "Slider2",
    Callback = function(Value)
        game.Players.LocalPlayer.Character:SetAttribute("DashLength", Value)
    end,
})
PlayerTab:CreateSlider({
    Name = "Jump Height",
    Range = {10, 500},
    Increment = 1,
    Suffix = "Height",
    CurrentValue = 10,
    Flag = "Slider3",
    Callback = function(Value)
        game.Players.LocalPlayer.Character.Humanoid.JumpPower = Value
    end,
})

local Toggle = PlayerTab:CreateToggle({
    Name = "Noclip",
    Callback = function(Value)
        if Value then
            noclipEnabled = true
            if not noclipCoroutine then
                noclipCoroutine = coroutine.create(function()
                    while noclipEnabled do
                        for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                            if part:IsA("BasePart") and part.CanCollide then
                                part.CanCollide = false
                            end
                        end
                        game:GetService("RunService").Stepped:Wait()
                    end
                    noclipCoroutine = nil
                end)
                coroutine.resume(noclipCoroutine)
            end
        else
            noclipEnabled = false
        end
    end,
})

local infiniteJumpEnabled = false

local infiniteJumpToggle = PlayerTab:CreateToggle({
    Name = "Infinite Jump",
    Callback = function(enabled)
        infiniteJumpEnabled = enabled
    end,
})

game:GetService("UserInputService").JumpRequest:Connect(function()
    if infiniteJumpEnabled then
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
            player.Character:FindFirstChildOfClass("Humanoid"):ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end
end)
local flyEnabled = false
local flySpeed = 50
local flyConnection

local function startFly()
    local player = game.Players.LocalPlayer
    local character = player.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    flyConnection = game:GetService("RunService").RenderStepped:Connect(function()
        if flyEnabled and hrp then
            local moveDirection = Vector3.new()
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.W) then
                moveDirection = moveDirection + hrp.CFrame.LookVector
            end
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.S) then
                moveDirection = moveDirection - hrp.CFrame.LookVector
            end
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.A) then
                moveDirection = moveDirection - hrp.CFrame.RightVector
            end
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.D) then
                moveDirection = moveDirection + hrp.CFrame.RightVector
            end
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.Space) then
                moveDirection = moveDirection + Vector3.new(0, 1, 0)
            end
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.LeftControl) then
                moveDirection = moveDirection - Vector3.new(0, 1, 0)
            end
            if moveDirection.Magnitude > 0 then
                hrp.Velocity = moveDirection.Unit * flySpeed
            else
                hrp.Velocity = Vector3.new(0, 0, 0)
            end
        end
    end)
end

local function stopFly()
    if flyConnection then
        flyConnection:Disconnect()
        flyConnection = nil
    end
    local player = game.Players.LocalPlayer
    local character = player.Character
    local hrp = character and character:FindFirstChild("HumanoidRootPart")
    if hrp then
        hrp.Velocity = Vector3.new(0, 0, 0)
    end
end

PlayerTab:CreateToggle({
    Name = "Fly",
    Callback = function(enabled)
        flyEnabled = enabled
        if enabled then
            startFly()
        else
            stopFly()
        end
    end,
})

PlayerTab:CreateSlider({
    Name = "Fly Speed",
    Range = {10, 300},
    Increment = 1,
    Suffix = "Speed",
    CurrentValue = flySpeed,
    Flag = "FlySpeedSlider",
    Callback = function(Value)
        flySpeed = Value
    end,
})
local Main = Window:CreateTab("Main", 4483362458)

local chestespenabled = true

Main:CreateToggle({
    Name = "Chest ESP",
    Callback = function(enabled)
        local chestNames = {"Chest", "TreasureChest", "BloxChest"}
        local espObjects = {}

        local function createESP(obj)
            if not obj:FindFirstChild("ESP") then
                local billboard = Instance.new("BillboardGui", obj)
                billboard.Name = "ESP"
                billboard.Size = UDim2.new(0, 100, 0, 40)
                billboard.Adornee = obj
                billboard.AlwaysOnTop = true

                local textLabel = Instance.new("TextLabel", billboard)
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.BackgroundTransparency = 1
                textLabel.Text = "Chest"
                textLabel.TextColor3 = Color3.new(0, 1, 0)
                textLabel.TextStrokeTransparency = 0.5
                textLabel.Font = Enum.Font.SourceSansBold
                textLabel.TextScaled = true

                espObjects[obj] = billboard
            end
        end

        local function removeESP()
            for obj, billboard in pairs(espObjects) do
                if billboard and billboard.Parent then
                    billboard:Destroy()
                end
            end
            espObjects = {}
        end

        if enabled then
            for _, chestName in ipairs(chestNames) do
                for _, obj in ipairs(workspace:GetChildren()) do
                    if obj.Name == chestName and obj:IsA("BasePart") then
                        createESP(obj)
                    end
                end
            end
            workspace.ChildAdded:Connect(function(obj)
                for _, chestName in ipairs(chestNames) do
                    if obj.Name == chestName and obj:IsA("BasePart") then
                        createESP(obj)
                    end
                end
            end)
        else
            removeESP()
        end
    end,
})
Main:CreateButton({
    Name = "Collect All Chests",
    Callback = function()
        local chestNames = {"Chest", "TreasureChest", "BloxChest"}
        local player = game.Players.LocalPlayer
        local character = player.Character
        local hrp = character and character:FindFirstChild("HumanoidRootPart")
        if not hrp then return end

        for _, chestName in ipairs(chestNames) do
            for _, obj in ipairs(workspace:GetChildren()) do
                if obj.Name == chestName and obj:IsA("BasePart") then
                    hrp.CFrame = obj.CFrame + Vector3.new(0, 3, 0)
                    wait(0.5)
                end
            end
        end
    end,
})

teleportFruit = Main:CreateToggle({
    Name = "Teleport to Fruit",
    Callback = function()
        local fruitNames = {"DevilFruit", "Fruit", "BloxFruit"}
        local player = game.Players.LocalPlayer
        for _, fruitName in ipairs(fruitNames) do
            for _, obj in ipairs(workspace:GetChildren()) do
                if obj.Name == fruitName and obj:IsA("BasePart") then
                    player.Character.HumanoidRootPart.CFrame = obj.CFrame + Vector3.new(0, 3, 0)
                    return
                end
            end
        end
    end,
})

Main:CreateToggle({
    Name = "ESP Devil Fruit", 
    Callback = function(enabled)
        local fruitNames = {"DevilFruit", "Fruit", "BloxFruit"}
        local espObjects = {}

        local function createESP(obj)
            if not obj:FindFirstChild("ESP") then
                local billboard = Instance.new("BillboardGui", obj)
                billboard.Name = "ESP"
                billboard.Size = UDim2.new(0, 100, 0, 40)
                billboard.Adornee = obj
                billboard.AlwaysOnTop = true

                local textLabel = Instance.new("TextLabel", billboard)
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.BackgroundTransparency = 1
                textLabel.Text = "Devil Fruit"
                textLabel.TextColor3 = Color3.new(1, 0.5, 0)
                textLabel.TextStrokeTransparency = 0.5
                textLabel.Font = Enum.Font.SourceSansBold
                textLabel.TextScaled = true

                espObjects[obj] = billboard
            end
        end

        local function removeESP()
            for obj, billboard in pairs(espObjects) do
                if billboard and billboard.Parent then
                    billboard:Destroy()
                end
            end
            espObjects = {}
        end

        if enabled then
            for _, fruitName in ipairs(fruitNames) do
                for _, obj in ipairs(workspace:GetChildren()) do
                    if obj.Name == fruitName and obj:IsA("BasePart") then
                        createESP(obj)
                    end
                end
            end
            workspace.ChildAdded:Connect(function(obj)
                for _, fruitName in ipairs(fruitNames) do
                    if obj.Name == fruitName and obj:IsA("BasePart") then
                        createESP(obj)
                    end
                end
            end)
        else
            removeESP()
        end
    end,
})

invisible = Main:CreateToggle({
    Name = "Invisible",
    Callback = function(Value)
        if Value then
            for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.Transparency = 1
                elseif part:IsA("Decal") then
                    part.Transparency = 1
                end
            end
        else
            for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.Transparency = 0
                elseif part:IsA("Decal") then
                    part.Transparency = 0
                end
            end
        end
    end,
})
autoFarm = Main:CreateToggle({
    Name = "Auto Farm",
    Callback = function()
        if autoFarm then
            autoFarm = false
            return
        end
        autoFarm = true
        while autoFarm do
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-34.5, 3.5, -0.5)
            wait(0.5)
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(34.5, 3.5, -0.5)
            wait(0.5)
        end
    end,
})

local Misc = Window:CreateTab("Misc", 4483362458)

teleport = Misc:CreateButton({
    Name = "Teleport to spawn",
    Callback = function()
        game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(0, 20, 0)
    end,
})
rejoin = Misc:CreateButton({
    Name = "Rejoin",
    Callback = function()
        game:GetService("TeleportService"):Teleport(game.PlaceId, game.Players.LocalPlayer)
    end,
})
exit = Misc:CreateButton({
    Name = "Exit",
    Callback = function()
        Rayfield:Destroy()
    end,
})

teleport = Misc:CreateButton({
    Name = "Server hop",
    Callback = function()
        local PlaceID = game.PlaceId
        local AllIDs = {}
        local foundAnything = ""
        local actualHour = os.date("!*t").hour
        local Deleted = false
        function TPReturner()
            local Site;
            if foundAnything == "" then
                Site = game.HttpService:JSONDecode(game:HttpGet('https://games.roblox.com/v1/games/' .. PlaceID .. '/servers/Public?sortOrder=Asc&limit=100'))
            else
                Site = game.HttpService:JSONDecode(game:HttpGet('https://games.roblox.com/v1/games/' .. PlaceID .. '/servers/Public?sortOrder=Asc&limit=100&cursor=' .. foundAnything))
            end
            local ID = ""
            if Site.nextPageCursor and Site.nextPageCursor ~= "null" and Site.nextPageCursor ~= nil then
                foundAnything = Site.nextPageCursor
            end
            local num = 0;
            for i, v in pairs(Site.data) do
                local Possible = true
                for _, Existing in pairs(AllIDs) do
                    if tonumber(v.id) == tonumber(Existing) then
                        Possible = false
                    end
                end
                if Possible and v.playing < v.maxPlayers and v.id ~= game.JobId then
                    table.insert(AllIDs, v.id)
                end
            end
        end
        function Teleport()
            while wait() do
                pcall(function()
                    TPReturner()
                    if #AllIDs > 0 then
                        wait()
                        local Random = math.random(1, #AllIDs)
                        game:GetService("TeleportService"):TeleportToPlaceInstance(PlaceID, AllIDs[Random], game.Players.LocalPlayer)
                        wait(4)
                    else
                        wait(2)
                    end
                end)
            end
        end
        Teleport()
    end,
})
bypasse = Misc:CreateButton({
    Name = "Bypass Anti-AFK",
    Callback = function()
        local vu = game:GetService("VirtualUser")
        game:GetService("Players").LocalPlayer.Idled:connect(function()
            vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
            wait(1)
            vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        end)
    end,
})
