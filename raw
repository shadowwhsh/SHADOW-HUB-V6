-- Ultra Aimbot & ESP Hub
-- https://github.com/shadowwhsh/ultra-aimbot-hub
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- Configurações
local fovPercent = 100
local camFov = 70
local espEnabled = false
local aimbotEnabled = false

-- UI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "HubUI"
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 160)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.Active, frame.Draggable = true, true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0,6)

-- Botão
local function mkBtn(txt, y)
  local b = Instance.new("TextButton", frame)
  b.Size = UDim2.new(1, -10, 0, 28)
  b.Position = UDim2.new(0, 5, 0, y)
  b.BackgroundColor3 = Color3.fromRGB(45,45,45)
  b.TextColor3 = Color3.new(1,1,1)
  b.Text, b.Font, b.TextSize = txt, Enum.Font.GothamBold, 16
  Instance.new("UICorner", b).CornerRadius = UDim.new(0,4)
  return b
end

local aimBtn = mkBtn("Aimbot: OFF", 10)
local espBtn = mkBtn("ESP: OFF", 44)
local fovInput = Instance.new("TextBox", frame)
fovInput.Size = UDim2.new(1, -10, 0, 28)
fovInput.Position = UDim2.new(0, 5, 0, 78)
fovInput.PlaceholderText = "FOV camera (40–200)"
fovInput.BackgroundColor3 = Color3.fromRGB(45,45,45)
fovInput.TextColor3 = Color3.new(1,1,1)
fovInput.Font, fovInput.TextSize = Enum.Font.GothamBold, 16
fovInput.ClearTextOnFocus = false

-- Toggle functions
aimBtn.MouseButton1Click:Connect(function()
  aimbotEnabled = not aimbotEnabled
  aimBtn.Text = "Aimbot: " .. (aimbotEnabled and "ON" or "OFF")
  aimBtn.BackgroundColor3 = aimbotEnabled and Color3.fromRGB(255,100,0) or Color3.fromRGB(45,45,45)
end)
espBtn.MouseButton1Click:Connect(function()
  espEnabled = not espEnabled
  espBtn.Text = "ESP: " .. (espEnabled and "ON" or "OFF")
  espBtn.BackgroundColor3 = espEnabled and Color3.fromRGB(0,150,255) or Color3.fromRGB(45,45,45)
end)
fovInput.FocusLost:Connect(function()
  local v = tonumber(fovInput.Text)
  if v and v >= 40 and v <= 200 then
    camFov = v
  end
end)

-- FOV circle
local fovCircle = Drawing.new("Circle")
fovCircle.Visible = true
fovCircle.Color = Color3.fromRGB(255,255,255)
fovCircle.Thickness = 1.5
fovCircle.Transparency = 0.6
fovCircle.Filled = false

-- ESP circles
local espDots = {}
local function addESP(plr)
  if plr == LocalPlayer then return end
  local d = Drawing.new("Circle")
  d.Radius, d.Filled = 4, true
  d.Visible = false
  espDots[plr] = d
end
for _,p in ipairs(Players:GetPlayers()) do addESP(p) end
Players.PlayerAdded:Connect(addESP)
Players.PlayerRemoving:Connect(function(p)
  if espDots[p] then espDots[p]:Remove(); espDots[p] = nil end
end)

-- Wall check
local function visible(part)
  local origin = Camera.CFrame.Position
  local dir = (part.Position - origin)
  local params = RaycastParams.new()
  params.FilterDescendantsInstances = {LocalPlayer.Character}
  params.FilterType = Enum.RaycastFilterType.Blacklist
  local res = workspace:Raycast(origin, dir, params)
  return (not res) or (res.Instance:IsDescendantOf(part.Parent))
end

-- Target selection
local function getTarget()
  local best, dist = nil, math.huge
  local vs = Camera.ViewportSize
  local center = Vector2.new(vs.X/2, vs.Y/2)
  -- real FOV circle radius based on percent:
  local maxr = (vs.Y/2)*(fovPercent/100)
  for _,plr in ipairs(Players:GetPlayers()) do
    if plr ~= LocalPlayer and plr.Team ~= LocalPlayer.Team and plr.Character and plr.Character:FindFirstChild("Head") then
      local sp, onScreen = Camera:WorldToViewportPoint(plr.Character.Head.Position)
      if onScreen then
        local d = (Vector2.new(sp.X, sp.Y)-center).Magnitude
        if d < maxr and d < dist and visible(plr.Character.Head) then
          best, dist = plr, d
        end
      end
    end
  end
  return best
end

-- Main loop
RunService.RenderStepped:Connect(function()
  Camera.FieldOfView = camFov
  local vs = Camera.ViewportSize
  fovCircle.Position = Vector2.new(vs.X/2, vs.Y/2)
  fovCircle.Radius = (vs.Y/2)*(fovPercent/100)

  if aimbotEnabled then
    local tgt = getTarget()
    if tgt then
      Camera.CFrame = CFrame.new(Camera.CFrame.Position, tgt.Character.Head.Position)
    end
  end

  for plr,d in pairs(espDots) do
    if espEnabled and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
      local sp, onScreen = Camera:WorldToViewportPoint(plr.Character.HumanoidRootPart.Position)
      if onScreen then
        d.Position = Vector2.new(sp.X, sp.Y)
        d.Color = plr.Team==LocalPlayer.Team and Color3.fromRGB(0,150,255) or Color3.fromRGB(255,60,60)
        d.Visible = true
      else
        d.Visible = false
      end
    else
      d.Visible = false
    end
  end
end)
