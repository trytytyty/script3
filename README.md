--[[
    Supreme Hub v2.5 Mobile PRO
    Game: Blox Fruits
]]
print("Supreme Hub v2.5: Script execution started. (Line 8)") -- PRINT INICIAL

--// Services
print("Supreme Hub: Attempting to load services... (Line 11)")
local Players, Workspace, ReplicatedStorage, VirtualUser, UserInputService, HttpService, TweenService, RunService
local successLoadServices, errorMsgServices = pcall(function()
    Players = game:GetService("Players")
    Workspace = game:GetService("Workspace")
    ReplicatedStorage = game:GetService("ReplicatedStorage")
    VirtualUser = game:GetService("VirtualUser")
    UserInputService = game:GetService("UserInputService")
    HttpService = game:GetService("HttpService")
    TweenService = game:GetService("TweenService")
    RunService = game:GetService("RunService")
end)

if not successLoadServices then
    print("Supreme Hub Critical Error: Failed to load Roblox services. Error: " .. tostring(errorMsgServices))
    warn("Supreme Hub Critical Error: Failed to load Roblox services. Error: " .. tostring(errorMsgServices))
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Supreme Hub FATAL ERROR", Text = "Failed to load services. Check console.", Duration = 20}) end)
    return
end
print("Supreme Hub: Services loaded successfully. (Line 30)")

--// Player Variables
local LocalPlayer = Players.LocalPlayer
if not LocalPlayer then
    print("Supreme Hub Critical Error: LocalPlayer not found. This is highly unusual. Script cannot continue.")
    warn("Supreme Hub Critical Error: LocalPlayer not found. This is highly unusual. Script cannot continue.")
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Supreme Hub FATAL ERROR", Text = "LocalPlayer not found. Check console.", Duration = 20}) end)
    return
end
print("Supreme Hub: LocalPlayer found: " .. LocalPlayer.Name .. " (Line 40)")

--// Main Configuration
local Config = {
    AutoFarmEnabled = false, SelectedWeapon = "Melee", AutoBringMob = true, AutoHaki = true, UseM1 = true, AutoSkills = true,
    SkillOrder = {"Z", "X", "C", "V", "F"}, SkillHoldTime = 0.2, SkillClickDelay = 0.5, FarmZone = "Current Island",
    LowHPSafeZone = true, LowHPPercentage = 30, PvPEnabled = false, PlayerRadar = true, AimPrediction = 0.135,
    TargetHPTrigger = 50, AutoWeaponSwitchPvP = true, SelectedPvPTarget = nil, FruitESP = true, TeleportToFruit = false,
    FruitTiers = {Low = true, Medium = true, Rare = true, Mythical = true, Permanent = true},
    SelectedIsland = "Middle Town", WalkSpeed = 16, WebhookURL = "", Region = "Current", AntiBan = true
}
print("Supreme Hub: Default configuration set. (Line 50)")

--// Library (Rayfield UI - Modified for Mobile)
print("Supreme Hub: Attempting to load Rayfield UI Library... (Line 53)")
local Rayfield
local successRayfieldDownload, rayfieldSourceOrError = pcall(function()
    return game:HttpGet('https://raw.githubusercontent.com/UI-Librarys/Rayfield/main/source', true)
end)

if not successRayfieldDownload or not rayfieldSourceOrError then
    print("Supreme Hub Critical Error: Failed to download Rayfield UI Library. Error: " .. tostring(rayfieldSourceOrError))
    warn("Supreme Hub Critical Error: Failed to download Rayfield UI Library. Error: " .. tostring(rayfieldSourceOrError))
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Supreme Hub Error", Text = "Failed to download UI Lib. Check console.", Duration = 15}) end)
    return
end
print("Supreme Hub: Rayfield UI source downloaded. Size: " .. string.len(rayfieldSourceOrError) .. " bytes. (Line 66)")

local successRayfieldLoad, loadedRayfieldFuncOrError = pcall(loadstring(rayfieldSourceOrError))
if not successRayfieldLoad or not loadedRayfieldFuncOrError then
    print("Supreme Hub Critical Error: Failed to load Rayfield UI Library from source. Error: " .. tostring(loadedRayfieldFuncOrError))
    warn("Supreme Hub Critical Error: Failed to load Rayfield UI Library from source. Error: " .. tostring(loadedRayfieldFuncOrError))
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Supreme Hub Error", Text = "Failed to load UI Lib code. Check console.", Duration = 15}) end)
    return
end
print("Supreme Hub: Rayfield UI source loaded into a function. (Line 76)")

local successRayfieldInit, rayfieldInstanceOrError = pcall(loadedRayfieldFuncOrError)
if not successRayfieldInit or not rayfieldInstanceOrError then
    print("Supreme Hub Critical Error: Failed to initialize Rayfield UI Library. Error: " .. tostring(rayfieldInstanceOrError))
    warn("Supreme Hub Critical Error: Failed to initialize Rayfield UI Library. Error: " .. tostring(rayfieldInstanceOrError))
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Supreme Hub Error", Text = "Failed to init UI Lib. Check console.", Duration = 15}) end)
    return
end
Rayfield = rayfieldInstanceOrError
print("Supreme Hub: Rayfield UI Library initialized successfully! (Line 87)")

--// Create Main Window
print("Supreme Hub: Attempting to create Rayfield Main Window... (Line 90)")
local Window
local successCreateWindow, windowOrError = pcall(function()
    Window = Rayfield:CreateWindow({
        Name = "Supreme Hub v2.5 (Test)", LoadingTitle = "Supreme Hub", LoadingSubtitle = "Loading...",
        ConfigurationSaving = {Enabled = true, FolderName = "SupremeHub_Test", FileName = "ConfigTest"}, KeySystem = false
    })
end)

if not successCreateWindow or not Window then
    print("Supreme Hub Critical Error: Failed to create Rayfield Main Window. Error: " .. tostring(windowOrError))
    warn("Supreme Hub Critical Error: Failed to create Rayfield Main Window. Error: " .. tostring(windowOrError))
    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Supreme Hub Error", Text = "Failed to create UI Window. Check console.", Duration = 15}) end)
    -- Não retornar aqui ainda, pode ser que Rayfield.Notify funcione mesmo se CreateWindow falhar parcialmente
    if Rayfield and Rayfield.Notify then
         pcall(function() Rayfield:Notify({Title = "UI Creation Failed", Content = "Main window creation failed. Check console.", Duration = 10}) end)
    end
    return -- Agora retorna, pois sem janela, o resto não adianta.
end
print("Supreme Hub: Main Rayfield window created. (Line 110)")

-- TESTE: Adicionar um botão simples para ver se a UI está minimamente funcional
local TestTab
local successCreateTestTab, testTabOrError = pcall(function()
    TestTab = Window:CreateTab("Test Tab", "rbxassetid://_") -- Ícone placeholder
end)

if not successCreateTestTab or not TestTab then
    print("Supreme Hub Warning: Failed to create Test Tab. Error: " .. tostring(testTabOrError))
    warn("Supreme Hub Warning: Failed to create Test Tab. Error: " .. tostring(testTabOrError))
else
    print("Supreme Hub: Test Tab created. Adding test button... (Line 123)")
    local successCreateTestButton, testButtonOrError = pcall(function()
        TestTab:CreateButton({
            Name = "Test UI Button - Click Me!",
            Callback = function()
                print("Supreme Hub: Test UI Button Clicked!")
                if Rayfield and Rayfield.Notify then
                    Rayfield:Notify({Title = "UI Test", Content = "Rayfield UI Button Clicked!", Duration = 5, Image = "rbxassetid://_"})
                else
                    pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title = "UI Test", Text = "UI Button Clicked!", Duration = 5}) end)
                end
            end,
        })
    end)
    if not successCreateTestButton then
        print("Supreme Hub Warning: Failed to create Test Button. Error: " .. tostring(testButtonOrError))
        warn("Supreme Hub Warning: Failed to create Test Button. Error: " .. tostring(testButtonOrError))
    else
        print("Supreme Hub: Test Button added to Test Tab. (Line 141)")
    end
end


--// Drawing API Check
if Drawing == nil or type(Drawing.new) ~= "function" then
    warn("Supreme Hub Warning: 'Drawing' API not found. ESP/Radar disabled.")
    Config.PlayerRadar = false ; Config.FruitESP = false
    if Rayfield and Rayfield.Notify then 
        pcall(function() Rayfield:Notify({Title = "Supreme Hub Warning", Content = "Drawing API not found. ESP & Radar disabled.", Duration = 10, Image = "rbxassetid://4483362458"}) end)
    else
        print("Supreme Hub Info: Drawing API not found. UI Notifier not ready.")
    end
else
    print("Supreme Hub: Drawing API found. (Line 155)")
end

--// Helper Functions (definições condensadas para brevidade aqui, pois o foco é a UI)
local function GetCharacter() return LocalPlayer.Character end
local function GetHumanoid() local char = GetCharacter() return char and char:FindFirstChildOfClass("Humanoid") end
local function GetRootPart() local char = GetCharacter() return char and char:FindFirstChild("HumanoidRootPart") end
local function SendWebhook(message) if Config.WebhookURL and string.find(Config.WebhookURL, "discord.com/api/webhooks") then pcall(function() HttpService:PostAsync(Config.WebhookURL, HttpService:JSONEncode({content = message})) end) end end
local function Notify(title, text, duration)
    if Rayfield and Rayfield.Notify then
        pcall(function() Rayfield:Notify({Title=title,Content=text,Duration=duration or 5,Image="rbxassetid://4483362458",Actions={Ignore={Name="Okay",Callback=function()end}}}) end)
    else
        print("Supreme Hub Notification (Rayfield.Notify N/A):", title, "-", text)
        pcall(function() game:GetService("StarterGui"):SetCore("SendNotification", {Title=title, Text=text, Duration=duration or 5}) end)
    end
end
local function AntiBanMeasures() if not Config.AntiBan then return end if LocalPlayer and LocalPlayer.Idled then pcall(function() VirtualUser:Button2Down(Vector2.new(0,0),Workspace.CurrentCamera.CFrame) task.wait(0.1) VirtualUser:Button2Up(Vector2.new(0,0),Workspace.CurrentCamera.CFrame) end) end task.wait(math.random(1,5)/10) end
print("Supreme Hub: Helper functions defined. (Line 172)")

-- Devido ao foco em fazer a UI aparecer, vou comentar o restante das abas e funcionalidades por enquanto.
-- Se a UI básica com o "Test Tab" e o botão aparecerem, podemos descomentar o resto gradualmente.

print("Supreme Hub: Skipping main feature tab creation for UI visibility test. (Line 177)")

--[[ -- INÍCIO DO BLOCO COMENTADO PARA TESTE DE UI

--==================================================================================================================--
--// TAB: Auto Farm
--==================================================================================================================--
print("Supreme Hub: Creating Auto Farm Tab...")
local FarmTab = Window:CreateTab("Auto Farm", "rbxassetid://4483362458")
-- ... (código da aba Auto Farm) ...
print("Supreme Hub: Auto Farm Tab and logic defined.")
--==================================================================================================================--
--// TAB: PvP
--==================================================================================================================--
print("Supreme Hub: Creating PvP Tab...")
local PvPTab = Window:CreateTab("PvP", "rbxassetid://4483362458")
-- ... (código da aba PvP) ...
print("Supreme Hub: PvP Tab and logic defined.")
--==================================================================================================================--
--// TAB: Fruits
--==================================================================================================================--
print("Supreme Hub: Creating Fruits Tab...")
local FruitTab = Window:CreateTab("Fruits", "rbxassetid://4483362458")
-- ... (código da aba Fruits) ...
print("Supreme Hub: Fruits Tab and logic defined.")
--==================================================================================================================--
--// TAB: Teleport
--==================================================================================================================--
print("Supreme Hub: Creating Teleport Tab...")
local TeleportTab = Window:CreateTab("Teleport", "rbxassetid://4483362458")
local IslandPositions = { -- Populating the previously declared table
    ["Middle Town"]=Vector3.new(-167,24,1572),["Jungle"]=Vector3.new(-1245,33,853),["Pirate Village"]=Vector3.new(777,25,2289),["Desert"]=Vector3.new(1402,17,-81),
    ["Frozen Village"]=Vector3.new(-1490,81,-1966),["Marine Fortress"]=Vector3.new(1549,90,4851),["Skylands"]=Vector3.new(-998,1420,2901),["Prison"]=Vector3.new(3573,28,642),
    ["Fountain City"]=Vector3.new(6200,90,4700),["Kingdom of Rose"]=Vector3.new(-990,246,-2148),["Green Zone"]=Vector3.new(2300,237,-3500),["Graveyard"]=Vector3.new(4510,230,-2900),
    ["Hot and Cold"]=Vector3.new(8970,230,-680),["Usoapp's Island"]=Vector3.new(5700,230,-5700),["Dark Arena"]=Vector3.new(3500,230,-6800),["Snowy Mountain"]=Vector3.new(7700,230,-2000), 
    ["Cursed Ship"]=Vector3.new(4500,230,-1500),["Ice Castle"]=Vector3.new(-2000,250,-3500),["Forgotten Island"]=Vector3.new(-3500,230,-500),["Port Town"]=Vector3.new(1250,235,6800),
    ["Hydra Island"]=Vector3.new(3400,245,8200),["Great Tree"]=Vector3.new(-1500,550,9500),["Floating Turtle"]=Vector3.new(-5400,800,11500),["Castle on the Sea"]=Vector3.new(6000,250,9500) 
}
-- ... (código da aba Teleport) ...
print("Supreme Hub: Teleport Tab and logic defined.")
--==================================================================================================================--
--// TAB: Settings
--==================================================================================================================--
print("Supreme Hub: Creating Settings Tab...")
local SettingsTab = Window:CreateTab("Settings", "rbxassetid://4483362458")
-- ... (código da aba Settings) ...
print("Supreme Hub: Settings Tab and logic defined.")

-- FIM DO BLOCO COMENTADO PARA TESTE DE UI --]]


--// Final initialization
print("Supreme Hub: Loading saved configuration (if any)... (Line 233)")
if Rayfield and Rayfield.LoadConfiguration then
    local successLoadConfig, errLoadConfig = pcall(function()
        Rayfield:LoadConfiguration()
    end)
    if successLoadConfig then
        print("Supreme Hub: Configuration loaded. (Line 240)")
    else
        print("Supreme Hub Warning: Failed to load configuration. Error: " .. tostring(errLoadConfig))
        warn("Supreme Hub Warning: Failed to load configuration. Error: " .. tostring(errLoadConfig))
    end
else
    print("Supreme Hub: Rayfield or LoadConfiguration not available, skipping config load.")
end

if GetHumanoid() and Config.WalkSpeed then 
    local s,e = pcall(function() GetHumanoid().WalkSpeed = Config.WalkSpeed end)
    if not s then print("Supreme Hub Warning: Failed to set initial walkspeed. Error: " .. tostring(e)) end
end

Notify("Supreme Hub UI Test", "Attempted to initialize UI. Check for a 'Test Tab'.", 10)
if Config.WebhookURL and string.find(Config.WebhookURL, "discord.com/api/webhooks") then 
    SendWebhook("User " .. LocalPlayer.Name .. " (" .. LocalPlayer.UserId .. ") loaded Supreme Hub v2.5 (UI Test Version)!")
end
print("Supreme Hub: Script execution finished. (Line 257) If UI is not visible, check console for errors above this line.")
