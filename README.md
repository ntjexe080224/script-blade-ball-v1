local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({
   Name = "minatoz hub V1",
   Icon = 0, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
   LoadingTitle = "loading script",
   LoadingSubtitle = "by minato",
   Theme = "Default", -- Check https://docs.sirius.menu/rayfield/configuration/themes

   ToggleUIKeybind = "K" -- The keybind to toggle the UI visibility (string like "K" or Enum.KeyCode)

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil, -- Create a custom folder for your hub/game
      FileName = "minatoz hub V1.txt"
   },

   Discord = {
      Enabled = true, -- Prompt the user to join your Discord server if their executor supports it
      Invite = "https://discord.gg/NYuByTAy", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ ABCD would be ABCD
      RememberJoins = true -- Set this to false to make them join the discord every time they load it up
   },

   KeySystem = true, -- Set this to true to use our key system
   KeySettings = {
      Title = "Untitled",
      Subtitle = "Key System",
      Note = "No method of obtaining the key is provided", -- Use this to tell the user how to get a key
      FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
      SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
      GrabKeyFromSite = true, -- https://discord.gg/NYuByTAy
      Key = {"minatobetav1"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
   }
})
local Tab = Window:CreateTab("main", 4483362458) -- Title, Image


local Toggle = Tab:CreateToggle({
   Name = "auto parry",
   CurrentValue = false,
   Flag = "getgenv().autoparry = false", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
   Callback = function(Value)
 local RunService = game:GetService("RunService") or game:FindFirstDescendant("RunService")
local Players = game:GetService("Players") or game:FindFirstDescendant("Players")
local VirtualInputManager = game:GetService("VirtualInputManager") or game:FindFirstDescendant("VirtualInputManager")

local Player = Players.LocalPlayer

local Cooldown = tick()
local IsParried = false
local Connection = nil

local function GetBall()
  for _, Ball in ipairs(workspace.Balls:GetChildren()) do
    if Ball:GetAttribute("realBall") then
      return Ball
    end
  end
end

local function ResetConnection()
    if Connection then
        Connection:Disconnect()
        Connection = nil
    end
end

workspace.Balls.ChildAdded:Connect(function()
    local Ball = GetBall()
    if not Ball then return end
    ResetConnection()
    Connection = Ball:GetAttributeChangedSignal("target"):Connect(function()
        Parried = false
    end)
end)

RunService.PreSimulation:Connect(function()
    local Ball, HRP = GetBall(), Player.Character.HumanoidRootPart
    if not Ball or not HRP then
      return
    end
    
    local Speed = Ball.zoomies.VectorVelocity.Magnitude
    local Distance = (HRP.Position - Ball.Position).Magnitude
    
    if Ball:GetAttribute("target") == Player.Name and not Parried and Distance / Speed <= 0.65 then
      VirtualInputManager:SendMouseButtonEvent(0, 0, 0, true, game, 0)
      Parried = true
      Cooldown = tick()
      
      if (tick() - Cooldown) >= 1 then
        Parried = false
      end
    end
end)
   end,
})
