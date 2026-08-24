-- Script Fuga CDT - Com Sistema de Senha
-- Senha: dzin23
-- ↓ = Salvar | ↑ = Teleportar

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
local PlayerGui = player:WaitForChild("PlayerGui")

local savedCFrame = nil
local unlocked = false
local correctPassword = "dzin23"

-- ========================
-- FUNÇÕES PRINCIPAIS
-- ========================

local function getCar()
	local char = player.Character
	if not char then return nil end
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return nil end
	local seat = hum.SeatPart
	if not seat then return nil end
	local root = seat.AssemblyRootPart
	if not root then return nil end
	return root:FindFirstAncestorWhichIsA("Model")
end

local function freezeCar(car, state)
	for _, v in ipairs(car:GetDescendants()) do
		if v:IsA("BasePart") then
			v.Anchored = state
			v.AssemblyLinearVelocity = Vector3.zero
			v.AssemblyAngularVelocity = Vector3.zero
		end
	end
end

local function notify(text)
	local n = Instance.new("TextLabel")
	n.Parent = PlayerGui
	n.Size = UDim2.new(0, 250, 0, 38)
	n.Position = UDim2.new(1, 300, 0.88, 0)
	n.BackgroundColor3 = Color3.fromRGB(20, 20, 26)
	n.TextColor3 = Color3.fromRGB(255, 255, 255)
	n.Text = text
	n.Font = Enum.Font.GothamMedium
	n.TextSize = 13
	n.BackgroundTransparency = 0.05
	Instance.new("UICorner", n).CornerRadius = UDim.new(0, 8)

	local stroke = Instance.new("UIStroke")
	stroke.Color = Color3.fromRGB(0, 140, 255)
	stroke.Thickness = 1.2
	stroke.Transparency = 0.6
	stroke.Parent = n

	local inTween = TweenService:Create(n, TweenInfo.new(0.35, Enum.EasingStyle.Quint), {
		Position = UDim2.new(1, -270, 0.88, 0)
	})
	local outTween = TweenService:Create(n, TweenInfo.new(0.35, Enum.EasingStyle.Quint), {
		Position = UDim2.new(1, 300, 0.88, 0)
	})

	inTween:Play()
	task.wait(1.9)
	outTween:Play()
	outTween.Completed:Wait()
	n:Destroy()
end

local function savePosition()
	if not unlocked then return end
	local car = getCar()
	if not car or not car.PrimaryPart then
		notify("Entre em um carro primeiro")
		return
	end
	savedCFrame = car.PrimaryPart.CFrame
	notify("Posição salva ✓")
end

local function teleportPosition()
	if not unlocked then return end
	if not savedCFrame then
		notify("Nenhuma posição salva")
		return
	end
	local car = getCar()
	if not car or not car.PrimaryPart then
		notify("Entre em um carro primeiro")
		return
	end
	freezeCar(car, true)
	car:SetPrimaryPartCFrame(savedCFrame)
	task.wait(0.50)
	freezeCar(car, false)
	notify("Teleportado ✓")
end

-- ========================
-- TELA DE SENHA
-- ========================

local passwordGui = Instance.new("ScreenGui")
passwordGui.Name = "PasswordGUI"
passwordGui.ResetOnSpawn = false
passwordGui.Parent = PlayerGui

local bg = Instance.new("Frame")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
bg.BackgroundTransparency = 0.3
bg.Parent = passwordGui

local passPanel = Instance.new("Frame")
passPanel.Size = UDim2.new(0, 280, 0, 180)
passPanel.Position = UDim2.new(0.5, -140, 0.5, -90)
passPanel.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
passPanel.Parent = passwordGui
Instance.new("UICorner", passPanel).CornerRadius = UDim.new(0, 12)

local passStroke = Instance.new("UIStroke")
passStroke.Color = Color3.fromRGB(0, 140, 255)
passStroke.Thickness = 1.5
passStroke.Parent = passPanel

local passTitle = Instance.new("TextLabel")
passTitle.Size = UDim2.new(1, 0, 0, 35)
passTitle.Position = UDim2.new(0, 0, 0, 12)
passTitle.BackgroundTransparency = 1
passTitle.Text = "DIGITE A SENHA"
passTitle.Font = Enum.Font.GothamBold
passTitle.TextSize = 16
passTitle.TextColor3 = Color3.fromRGB(0, 170, 255)
passTitle.Parent = passPanel

local passBox = Instance.new("TextBox")
passBox.Size = UDim2.new(1, -40, 0, 38)
passBox.Position = UDim2.new(0, 20, 0, 60)
passBox.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
passBox.Text = ""
passBox.PlaceholderText = "Senha..."
passBox.Font = Enum.Font.Gotham
passBox.TextSize = 14
passBox.TextColor3 = Color3.new(1, 1, 1)
passBox.ClearTextOnFocus = false
passBox.Parent = passPanel
Instance.new("UICorner", passBox).CornerRadius = UDim.new(0, 8)

local confirmBtn = Instance.new("TextButton")
confirmBtn.Size = UDim2.new(1, -40, 0, 38)
confirmBtn.Position = UDim2.new(0, 20, 0, 115)
confirmBtn.Text = "Confirmar"
confirmBtn.Font = Enum.Font.GothamBold
confirmBtn.TextSize = 14
confirmBtn.TextColor3 = Color3.new(1, 1, 1)
confirmBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
confirmBtn.Parent = passPanel
Instance.new("UICorner", confirmBtn).CornerRadius = UDim.new(0, 8)

-- ========================
-- GUI PRINCIPAL
-- ========================

local mainGui = Instance.new("ScreenGui")
mainGui.Name = "FugaGUI"
mainGui.ResetOnSpawn = false
mainGui.Enabled = false
mainGui.Parent = PlayerGui

local panel = Instance.new("Frame")
panel.Size = UDim2.new(0, 190, 0, 155)
panel.Position = UDim2.new(1, -210, 0.16, 0)
panel.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
panel.BackgroundTransparency = 0.05
panel.Parent = mainGui
Instance.new("UICorner", panel).CornerRadius = UDim.new(0, 12)

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(0, 140, 255)
stroke.Thickness = 1.4
stroke.Transparency = 0.55
stroke.Parent = panel

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 26)
title.Position = UDim2.new(0, 0, 0, 8)
title.BackgroundTransparency = 1
title.Text = "FUGA SCRIPT"
title.Font = Enum.Font.GothamBold
title.TextSize = 15
title.TextColor3 = Color3.fromRGB(0, 170, 255)
title.Parent = panel

local keys = Instance.new("TextLabel")
keys.Size = UDim2.new(1, 0, 0, 18)
keys.Position = UDim2.new(0, 0, 0, 32)
keys.BackgroundTransparency = 1
keys.Text = "↓ Salvar   |   ↑ Teleportar"
keys.Font = Enum.Font.Gotham
keys.TextSize = 11
keys.TextColor3 = Color3.fromRGB(160, 160, 175)
keys.Parent = panel

local saveBtn = Instance.new("TextButton")
saveBtn.Size = UDim2.new(1, -20, 0, 36)
saveBtn.Position = UDim2.new(0, 10, 0, 58)
saveBtn.Text = "Salvar Posição"
saveBtn.Font = Enum.Font.GothamBold
saveBtn.TextSize = 13
saveBtn.TextColor3 = Color3.new(1, 1, 1)
saveBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
saveBtn.Parent = panel
Instance.new("UICorner", saveBtn).CornerRadius = UDim.new(0, 8)

local backBtn = Instance.new("TextButton")
backBtn.Size = UDim2.new(1, -20, 0, 36)
backBtn.Position = UDim2.new(0, 10, 0, 102)
backBtn.Text = "Teleportar"
backBtn.Font = Enum.Font.GothamBold
backBtn.TextSize = 13
backBtn.TextColor3 = Color3.new(1, 1, 1)
backBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
backBtn.Parent = panel
Instance.new("UICorner", backBtn).CornerRadius = UDim.new(0, 8)

saveBtn.MouseButton1Click:Connect(savePosition)
backBtn.MouseButton1Click:Connect(teleportPosition)

-- ========================
-- LÓGICA DA SENHA
-- ========================

local function unlockScript()
	unlocked = true
	passwordGui:Destroy()
	mainGui.Enabled = true
	notify("Acesso liberado ✓")
	print("[Fuga] Senha correta - Script liberado")
end

local function wrongPassword()
	notify("SENHA INCORRETA")
	passBox.Text = ""
end

confirmBtn.MouseButton1Click:Connect(function()
	if passBox.Text == correctPassword then
		unlockScript()
	else
		wrongPassword()
	end
end)

passBox.FocusLost:Connect(function(enter)
	if enter then
		if passBox.Text == correctPassword then
			unlockScript()
		else
			wrongPassword()
		end
	end
end)

-- ========================
-- TECLADO
-- ========================

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed or not unlocked then return end

	if input.KeyCode == Enum.KeyCode.Down then
		savePosition()
	elseif input.KeyCode == Enum.KeyCode.Up then
		teleportPosition()
	end
end)

print("[Fuga CDT] Aguardando senha...")
