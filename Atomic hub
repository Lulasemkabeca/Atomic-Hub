local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()

local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "AtomicHub"

local toggleBtn = Instance.new("TextButton", gui)
toggleBtn.Size = UDim2.new(0, 50, 0, 50)
toggleBtn.Position = UDim2.new(0, 10, 0.5, -25)
toggleBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
toggleBtn.Text = "⚛"
toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleBtn.Font = Enum.Font.GothamBold
toggleBtn.TextSize = 30
toggleBtn.Active = true
toggleBtn.Draggable = true

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 210)
frame.Position = UDim2.new(0, 70, 0.5, -105)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true

local function newButton(text, y)
	local b = Instance.new("TextButton", frame)
	b.Size = UDim2.new(1, -20, 0, 40)
	b.Position = UDim2.new(0, 10, 0, y)
	b.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
	b.TextColor3 = Color3.new(1, 1, 1)
	b.Font = Enum.Font.SourceSansBold
	b.TextSize = 16
	b.Text = text
	return b
end

local autoFarmBtn = newButton("Atomic Hub: Ativar", 10)
local dashBtn = newButton("Mini Teleporte", 60)
local bossBtn = newButton("Teleport Boss: Ativar", 110)
local speedBtn = newButton("Velocidade: 50 (Off)", 160)

toggleBtn.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

local ativo = false
local teleportBossAtivo = false
local speedAtivo = false
local savedCFrame = nil
local fixo, giro, atualAlvo = nil, nil, nil

local function detectarDistanciaIdeal()
	local tool = char and char:FindFirstChildOfClass("Tool")
	if tool then
		local n = tool.Name:lower()
		if n:find("adaga") or n:find("dagger") then return 6 end
		if n:find("espada") or n:find("sword") then return 9 end
	end
	return 7
end

local function destravar()
	if fixo then fixo:Destroy() fixo = nil end
	if giro then giro:Destroy() giro = nil end
	local hum = char:FindFirstChild("Humanoid")
	if hum then hum.PlatformStand = false end
end

local function travarEmbaixo(alvo)
	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return end
	destravar()
	local d = detectarDistanciaIdeal()
	local pos = alvo.HumanoidRootPart.Position - Vector3.new(0, d - 0.5, 0)
	fixo = Instance.new("BodyPosition", root)
	fixo.MaxForce = Vector3.new(1e9, 1e9, 1e9)
	fixo.P = 1e5
	fixo.D = 1000
	fixo.Position = pos
	giro = Instance.new("BodyGyro", root)
	giro.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
	giro.P = 3000
	giro.D = 150
	giro.CFrame = CFrame.new(pos) * CFrame.Angles(math.rad(90), 0, 0)
	char.Humanoid.PlatformStand = true
	root.CFrame = CFrame.new(pos) * CFrame.Angles(math.rad(90), 0, 0)
	atualAlvo = alvo
end

local function getClosestEnemy()
	local root = char:FindFirstChild("HumanoidRootPart")
	if not root then return nil end
	local best, dist = nil, math.huge
	for _, h in ipairs(workspace:GetDescendants()) do
		if h:IsA("Humanoid") and h.Health > 0 then
			local m = h.Parent
			if m:IsA("Model") and m:FindFirstChild("HumanoidRootPart") and not game.Players:GetPlayerFromCharacter(m) and m ~= char then
				local d = (root.Position - m.HumanoidRootPart.Position).Magnitude
				if d < dist then
					best, dist = m, d
				end
			end
		end
	end
	return best
end

local function getBoss()
	local boss, maxHP = nil, 0
	for _, h in ipairs(workspace:GetDescendants()) do
		if h:IsA("Humanoid") and h.Health > 0 then
			local m = h.Parent
			if m:IsA("Model") and m:FindFirstChild("HumanoidRootPart") and not game.Players:GetPlayerFromCharacter(m) then
				if h.MaxHealth > maxHP then
					boss, maxHP = m, h.MaxHealth
				end
			end
		end
	end
	return boss
end

task.spawn(function()
	while true do
		task.wait(0.2)
		if ativo then
			local alvo = getClosestEnemy()
			if alvo and alvo:FindFirstChild("Humanoid") and alvo.Humanoid.Health > 0 then
				travarEmbaixo(alvo)
				while ativo and alvo and alvo:FindFirstChild("Humanoid") and alvo.Humanoid.Health > 0 do
					task.wait(0.2)
				end
				destravar()
				atualAlvo = nil
			end
		end
	end
end)

-- ✅ VELOCIDADE FIXA 50
local RunService = game:GetService("RunService")
RunService.Heartbeat:Connect(function()
	if speedAtivo and char and char:FindFirstChild("Humanoid") then
		char.Humanoid.WalkSpeed = 50
	end
end)

-- ✅ AUTO FARM COM PORTAL
autoFarmBtn.MouseButton1Click:Connect(function()
	ativo = not ativo
	autoFarmBtn.Text = ativo and "Atomic Hub: Desativar" or "Atomic Hub: Ativar"
	autoFarmBtn.BackgroundColor3 = ativo and Color3.fromRGB(60, 60, 60) or Color3.fromRGB(100, 100, 100)

	if ativo then
		local PORTAL_ID  = "1350001"
		local DOOR_NAME  = "Door"
		local offsetDist = 2.5

		local portal = workspace:FindFirstChild(PORTAL_ID)
		if portal then
			local door = portal:FindFirstChild(DOOR_NAME)
			if door and char and char:FindFirstChild("HumanoidRootPart") then
				local hrp = char.HumanoidRootPart
				local targetPos = door.Position - door.CFrame.LookVector * offsetDist
				hrp.CFrame = CFrame.new(targetPos, door.Position)
				task.wait(1.5)
			end
		end
	else
		destravar()
		atualAlvo = nil
	end
end)

dashBtn.MouseButton1Click:Connect(function()
	local root = char:FindFirstChild("HumanoidRootPart")
	if root then
		root.CFrame = root.CFrame * CFrame.new(0, 0, -12)
	end
end)

bossBtn.MouseButton1Click:Connect(function()
	teleportBossAtivo = not teleportBossAtivo
	bossBtn.Text = teleportBossAtivo and "Teleport Boss: Desativar" or "Teleport Boss: Ativar"
	if teleportBossAtivo then
		local root = char:FindFirstChild("HumanoidRootPart")
		local boss = getBoss()
		if root and boss and boss:FindFirstChild("HumanoidRootPart") then
			savedCFrame = root.CFrame
			local d = detectarDistanciaIdeal()
			root.CFrame = boss.HumanoidRootPart.CFrame + boss.HumanoidRootPart.CFrame.LookVector * d + Vector3.new(0, 0.5, 0)
		end
	elseif savedCFrame then
		local root = char:FindFirstChild("HumanoidRootPart")
		if root then
			root.CFrame = savedCFrame
		end
		savedCFrame = nil
	end
end)

speedBtn.MouseButton1Click:Connect(function()
	speedAtivo = not speedAtivo
	speedBtn.Text = speedAtivo and "Velocidade: 50 (On)" or "Velocidade: 50 (Off)"
	if not speedAtivo and char and char:FindFirstChild("Humanoid") then
		char.Humanoid.WalkSpeed = 16
	end
end)
