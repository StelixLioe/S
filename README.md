local Players = game:GetService("Players")
local VirtualUser = game:GetService("VirtualUser")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

local TARGET_STAND = "One for All [Stage 4]"
local PREVIOUS_STAGE = "One for All [Stage 3]"

local TARGETS = {
    "Deku",
    "Roland",
    "AngelicaWeak"
}

player.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end)

local function getStand()
    local data = player:FindFirstChild("Data")
    if data and data:FindFirstChild("StandName") then
        return data.StandName.Value
    end
    return nil
end

local function useGrace()
    local graceBackpack = player.Backpack:FindFirstChild("OA's Grace")
    local graceWorld = workspace:FindFirstChild("Item2") and workspace.Item2:FindFirstChild("OA's Grace")

    if graceBackpack and character:FindFirstChild("Humanoid") then
        character.Humanoid:EquipTool(graceBackpack)
        graceBackpack:Activate()
    elseif graceWorld and graceWorld:FindFirstChild("Handle") then
        firetouchinterest(character.HumanoidRootPart, graceWorld.Handle, 0)
        firetouchinterest(character.HumanoidRootPart, graceWorld.Handle, 1)
    end
end

local function teleportTo(target)
    if target and target:FindFirstChild("HumanoidRootPart") then
        character.HumanoidRootPart.CFrame = target.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
    end
end

local function instakill(humanoid)
    if humanoid and humanoid.Health > 0 then
        humanoid.Health = 0
    end
end

local function spawnDeku()
    local spawn = workspace:FindFirstChild("Map")
    if spawn and spawn:FindFirstChild("RuinedCity") then
        local prompt = spawn.RuinedCity.Spawn:FindFirstChild("ProximityPrompt")
        if prompt then
            fireproximityprompt(prompt)
        end
    end
end

local function spawnRoland()
    local spawn = workspace:FindFirstChild("Map")
    if spawn and spawn:FindFirstChild("RuinedCity") then
        local prompt = spawn.RuinedCity.Spawn:FindFirstChild("ProximityPromptB")
        if prompt then
            fireproximityprompt(prompt)
        end
    end
end

local function pickupGloves()
    local itemFolder = workspace:FindFirstChild("Item")
    if itemFolder then
        local gloves = itemFolder:FindFirstChild("Gloves")
        if gloves and gloves:FindFirstChild("Handle") then
            firetouchinterest(character.HumanoidRootPart, gloves.Handle, 0)
            firetouchinterest(character.HumanoidRootPart, gloves.Handle, 1)
        end
    end
end

while task.wait(0.4) do
    character = player.Character or player.CharacterAdded:Wait()

    local stand = getStand()

    if stand == PREVIOUS_STAGE then
        useGrace()
        task.wait(2)
    end

    if stand == TARGET_STAND then
        spawnDeku()
        task.wait(2)

        spawnRoland()
        task.wait(2)

        local living = workspace:FindFirstChild("Living")

        if living then
            for _, name in ipairs(TARGETS) do
                local npc = living:FindFirstChild(name)

                if npc and not npc:FindFirstChild("NewHandler") then
                    local humanoid = npc:FindFirstChildOfClass("Humanoid")

                    if humanoid and humanoid.Health > 0 then
                        teleportTo(npc)
                        task.wait(0.1)
                        instakill(humanoid)
                    end
                end
            end
        end

        pickupGloves()
    end
end
