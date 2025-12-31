--==================================================
-- 🔥 BAIANO HUB | BLOX FRUITS
-- 👑 by Richarlyson
-- Interface: RedzLib V4
--==================================================

-- Carregar RedzLib V4
local Library = loadstring(game:HttpGet(
    "https://raw.githubusercontent.com/wx-sources/RedzLibV4/main/Source.lua"
))()

-- Serviços
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local VirtualUser = game:GetService("VirtualUser")

local LocalPlayer = Players.LocalPlayer

--==================================================
-- JANELA
--==================================================
local Window = Library:MakeWindow({
    Title = "🔥 BAIANO HUB",
    SubTitle = "Blox Fruits | by Richarlyson",
    LoadText = "Carregando BAIANO HUB...",
    Flags = "BaianoHubBloxFruitsV1"
})

Library:SetTheme("Default")
Library:SetTransparency(0.1)

Library:MakeNotify({
    Title = "BAIANO HUB",
    Text = "Hub carregado com sucesso!",
    Time = 5
})

--==================================================
-- VARIÁVEIS
--==================================================
local AutoFarm = false
local AutoBoss = false

--==================================================
-- FUNÇÕES AUX
--==================================================
local function getChar()
    return LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
end

local function getHRP()
    return getChar():WaitForChild("HumanoidRootPart")
end

local function EquipWeapon()
    for _,v in pairs(LocalPlayer.Backpack:GetChildren()) do
        if v:IsA("Tool") then
            v.Parent = getChar()
            break
        end
    end
end

local function FastAttack()
    VirtualUser:Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
    task.wait()
    VirtualUser:Button1Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
end

local function BringAll(target)
    for _,mob in pairs(Workspace.Enemies:GetChildren()) do
        if mob ~= target
        and mob:FindFirstChild("HumanoidRootPart")
        and mob:FindFirstChild("Humanoid")
        and mob.Humanoid.Health > 0 then
            mob.HumanoidRootPart.CFrame = target.HumanoidRootPart.CFrame
            mob.HumanoidRootPart.CanCollide = false
            mob.Humanoid.WalkSpeed = 0
            mob.Humanoid.JumpPower = 0
            mob.Humanoid:ChangeState(11)
        end
    end
end

--==================================================
-- TAB FARM
--==================================================
local TabFarm = Window:MakeTab({
    Name = "⚔️ Farm",
    Icon = "Sword"
})

TabFarm:AddToggle({
    Name = "🔥 Auto Farm Level (GOD)",
    Callback = function(state)
        AutoFarm = state
        task.spawn(function()
            while AutoFarm do
                pcall(function()
                    EquipWeapon()
                    local hrp = getHRP()

                    for _,mob in pairs(Workspace.Enemies:GetChildren()) do
                        if mob:FindFirstChild("Humanoid")
                        and mob.Humanoid.Health > 0
                        and mob:FindFirstChild("HumanoidRootPart") then
                            
                            hrp.CFrame = mob.HumanoidRootPart.CFrame * CFrame.new(0, 12, 0)

                            mob.HumanoidRootPart.CanCollide = false
                            mob.Humanoid.WalkSpeed = 0
                            mob.Humanoid.JumpPower = 0
                            mob.Humanoid:ChangeState(11)

                            BringAll(mob)

                            repeat
                                FastAttack()
                                task.wait(0.05)
                            until mob.Humanoid.Health <= 0 or not AutoFarm
                        end
                    end
                end)
                task.wait()
            end
        end)
    end
})

-- Anti-stuck
RunService.Stepped:Connect(function()
    if AutoFarm then
        pcall(function()
            getHRP().Velocity = Vector3.zero
        end)
    end
end)

--==================================================
-- TAB TELEPORT
--==================================================
local TabTP = Window:MakeTab({
    Name = "🗺️ Teleport",
    Icon = "Map"
})

local Islands = {
    ["Middle Town"] = CFrame.new(-690,15,1583),
    ["Jungle"] = CFrame.new(-1612,36,149),
    ["Colosseum"] = CFrame.new(-1427,7,-2792),
    ["Marineford"] = CFrame.new(-4914,50,4284)
}

TabTP:AddDropdown({
    Name = "Selecionar Ilha",
    Options = {"Middle Town","Jungle","Colosseum","Marineford"},
    Callback = function(v)
        getHRP().CFrame = Islands[v]
    end
})

--==================================================
-- TAB STATUS
--==================================================
local TabStats = Window:MakeTab({
    Name = "📊 Status",
    Icon = "BarChart"
})

local function addStat(stat)
    ReplicatedStorage.Remotes.CommF_:InvokeServer("AddPoint", stat, 1)
end

TabStats:AddButton({ Name = "Upar Melee", Callback = function() addStat("Melee") end })
TabStats:AddButton({ Name = "Upar Defense", Callback = function() addStat("Defense") end })
TabStats:AddButton({ Name = "Upar Sword", Callback = function() addStat("Sword") end })
TabStats:AddButton({ Name = "Upar Gun", Callback = function() addStat("Gun") end })
TabStats:AddButton({ Name = "Upar Blox Fruit", Callback = function() addStat("Demon Fruit") end })

--==================================================
-- TAB BOSS
--==================================================
local TabBoss = Window:MakeTab({
    Name = "👑 Boss",
    Icon = "Crown"
})

TabBoss:AddToggle({
    Name = "Auto Boss (perto)",
    Callback = function(state)
        AutoBoss = state
        task.spawn(function()
            while AutoBoss do
                pcall(function()
                    EquipWeapon()
                    local hrp = getHRP()
                    for _,boss in pairs(Workspace.Enemies:GetChildren()) do
                        if boss:FindFirstChild("Humanoid")
                        and boss.Humanoid.Health > 0
                        and boss.Name:lower():find("boss") then
                            hrp.CFrame = boss.HumanoidRootPart.CFrame * CFrame.new(0,12,0)
                            repeat
                                FastAttack()
                                task.wait(0.05)
                            until boss.Humanoid.Health <= 0 or not AutoBoss
                        end
                    end
                end)
                task.wait()
            end
        end)
    end
})

--==================================================
-- TAB SETTINGS
--==================================================
local TabSettings = Window:MakeTab({
    Name = "⚙️ Settings",
    Icon = "Settings"
})

TabSettings:AddButton({
    Name = "Reentrar no Jogo",
    Callback = function()
        game:GetService("TeleportService"):Teleport(game.PlaceId, LocalPlayer)
    end
})

TabSettings:AddButton({
    Name = "Fechar Hub",
    Callback = function()
        Library:Destroy()
    end
})
