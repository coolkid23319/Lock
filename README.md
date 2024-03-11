local Notification = loadstring(game:HttpGet("https://raw.githubusercontent.com/BocusLuke/UI/main/STX/Client.Lua"))()

local Settings = {
    rewrittenmain = {
        Enabled = true,
        Key = "c",
        DOT = true,
        AIRSHOT = true,
        NOTIF = true,
        AUTOPRED = true,
        FOV = math.huge,
        RESOVLER = false
    }
}

local SelectedPart = "HumanoidRootPart"
local Prediction = true
local PredictionValue = 0.1111

local CC = game:GetService("Workspace").CurrentCamera
local Plr
local enabled = false
local accomidationfactor = 0.121
local mouse = game.Players.LocalPlayer:GetMouse()
local placemarker = Instance.new("Part", game.Workspace)

function makemarker(Parent, Adornee, Color, Size, Size2)
    local e = Instance.new("BillboardGui", Parent)
    e.Name = "PP"
    e.Adornee = Adornee
    e.Size = UDim2.new(Size, Size2, Size, Size2)
    e.AlwaysOnTop = Settings.rewrittenmain.DOT
    local a = Instance.new("Frame", e)
    a.Size = UDim2.new(Settings.rewrittenmain.DOT and 1 or 0, 1, Settings.rewrittenmain.DOT and 1 or 0, 1)
    a.Transparency = Settings.rewrittenmain.DOT and 0 or 1
    a.BackgroundTransparency = Settings.rewrittenmain.DOT and 0 or 1
    a.BackgroundColor3 = Color
    local g = Instance.new("UICorner", a)
    g.CornerRadius = UDim.new(1, 1)
    return e
end

local function noob(player)
    local handler = makemarker(guimain, player.Character:WaitForChild(SelectedPart), Color3.fromRGB(107, 184, 255), 0.3, 3)
    handler.Name = player.Name
    player.CharacterAdded:Connect(function(Char)
        handler.Adornee = Char:WaitForChild(SelectedPart)
    end)
end

for _, player in ipairs(game.Players:GetPlayers()) do
    if player ~= game.Players.LocalPlayer then
        noob(player)
    end
end

game.Players.PlayerAdded:Connect(noob)

spawn(function()
    placemarker.Anchored = true
    placemarker.CanCollide = false
    placemarker.Size = Vector3.new(0, 0, 0)
    placemarker.Transparency = 0.75
    if Settings.rewrittenmain.DOT then
        makemarker(placemarker, placemarker, Color3.fromRGB(0, 0, 139), 1, 0)
    end
end)

game.Players.LocalPlayer:GetMouse().KeyDown:Connect(function(k)
    if k == Settings.rewrittenmain.Key and Settings.rewrittenmain.Enabled then
        if enabled then
            enabled = false
            if Settings.rewrittenmain.NOTIF then
                Notification:Notify(
                    { Title = "FrostByte", Description = "Unlocked" },
                    { OutlineColor = Color3.fromRGB(0, 73, 168), Time = 5, Type = "option" },
                    { Image = "http://www.roblox.com/asset/?id=6023426923", ImageColor = Color3.fromRGB(0, 73, 168) }
                )
            end
        else
            Plr = getClosestPlayerToCursor()
            enabled = true
            if Settings.rewrittenmain.NOTIF then
                Notification:Notify(
                    { Title = "FrostByte", Description = "Locked: " .. tostring(Plr.Character.Humanoid.DisplayName) },
                    { OutlineColor = Color3.fromRGB(0, 73, 168), Time = 5, Type = "option" },
                    { Image = "http://www.roblox.com/asset/?id=6023426923", ImageColor = Color3.fromRGB(0, 73, 168) }
                )
            end
        end
    end
end)

function getClosestPlayerToCursor()
    local closestPlayer
    local shortestDistance = Settings.rewrittenmain.FOV

    for _, v in ipairs(game.Players:GetPlayers()) do
        if v ~= game.Players.LocalPlayer and v.Character and v.Character:FindFirstChild("Humanoid") and v.Character.Humanoid.Health ~= 0 and v.Character:FindFirstChild("HumanoidRootPart") then
            local pos = CC:WorldToViewportPoint(v.Character.PrimaryPart.Position)
            local magnitude = (Vector2.new(pos.X, pos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude
            if magnitude < shortestDistance then
                closestPlayer = v
                shortestDistance = magnitude
            end
        end
    end
    return closestPlayer
end

local pingvalue = nil
local split = nil
local ping = nil

game:GetService("RunService").Stepped:Connect(function()
    if enabled and Plr and Plr.Character and Plr.Character:FindFirstChild("HumanoidRootPart") then
        placemarker.CFrame = CFrame.new(Plr.Character.HumanoidRootPart.Position + (Plr.Character.HumanoidRootPart.Velocity * accomidationfactor))
    else
        placemarker.CFrame = CFrame.new(0, 9999, 0)
    end
    if Settings.rewrittenmain.AUTOPRED then
        pingvalue = game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValueString()
        split = string.split(pingvalue, '(')
        ping = tonumber(split[1])
        if ping < 130 then
            PredictionValue = 0.150
        elseif ping < 125 then
            PredictionValue = 0.16
        elseif ping < 110 then
            PredictionValue = 0.15
        elseif ping < 105 then
            PredictionValue = 0.15
        elseif ping < 90 then
            PredictionValue = 0.1482
        elseif ping < 80 then
            PredictionValue = 0.142
        elseif ping < 70 then
            PredictionValue = 0.142
        elseif ping < 60 then
            PredictionValue = 0.12731
        elseif ping < 50 then
            PredictionValue = 0.125
        elseif ping < 40 then
            PredictionValue = 0.1325
        elseif ping < 30 then
            PredictionValue = 0.113
        elseif ping < 20 then
            PredictionValue = 0.112
        elseif ping < 10 then
            PredictionValue = 0.085
        end
    end
end)

local mt = getrawmetatable(game)
local old = mt.__namecall
setreadonly(mt, false)
mt.__namecall = newcclosure(function(...)
    local args = { ... }
    if enabled and getnamecallmethod() == "Fire Server" and args[2] == "UpdateMousePos" and Settings.rewrittenmain.Enabled and Plr and Plr.Character and Plr.Character:FindFirstChild(SelectedPart) then
        if Prediction then
            args[3] = Plr.Character[SelectedPart].Position + (Plr.Character[SelectedPart].Velocity * PredictionValue)
        else
            args[3] = Plr.Character[SelectedPart].Position
        end
        return old(unpack(args))
    end
    return old(...)
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if Settings.rewrittenmain.RESOVLER and Plr and Plr.Character and enabled and Settings.rewrittenmain.Enabled then
        if Settings.rewrittenmain.AIRSHOT and enabled and Plr.Character then
            local state = game.Workspace.Players[Plr.Name].Humanoid:GetState()
            if state == Enum.HumanoidStateType.Freefall then
                SelectedPart = "LeftFoot"
            else
                SelectedPart = "HumanoidRootPart"
            end
        else
            SelectedPart = "HumanoidRootPart"
        end
    end
end)

loadstring(game:HttpGet("https://raw.githubusercontent.com/therealzeek/Ctool/main/README.lua", true))()