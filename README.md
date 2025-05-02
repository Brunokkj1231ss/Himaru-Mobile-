-- LocalScript em StarterPlayerScripts

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

-- Criar a ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "lifeHubV1"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- Frame Principal
local hubFrame = Instance.new("Frame")
hubFrame.Size = UDim2.new(0, 600, 0, 350)
hubFrame.Position = UDim2.new(0.5, -300, 0.5, -175)
hubFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
hubFrame.BorderSizePixel = 0
hubFrame.Parent = gui

-- Variáveis para armazenar o tamanho original do frame
local originalSize = hubFrame.Size
local originalPosition = hubFrame.Position
local minimized = false

-- Cantos Arredondados
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = hubFrame

-- Borda RGB animada
local stroke = Instance.new("UIStroke")
stroke.Thickness = 4
stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
stroke.Color = Color3.fromRGB(255, 0, 0)
stroke.Parent = hubFrame

local hue = 0
RunService.RenderStepped:Connect(function()
	hue = (hue + 0.005) % 1
	stroke.Color = Color3.fromHSV(hue, 1, 1)
end)

-- TABS (Somente a aba "MAIN")
local tabs = {}

local tabButtonsFrame = Instance.new("Frame")
tabButtonsFrame.Size = UDim2.new(1, 0, 0, 40)
tabButtonsFrame.Position = UDim2.new(0, 0, 0, 50)
tabButtonsFrame.BackgroundTransparency = 1
tabButtonsFrame.Parent = hubFrame

local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, -20, 1, -100)
contentFrame.Position = UDim2.new(0, 10, 0, 90)
contentFrame.BackgroundTransparency = 1
contentFrame.Name = "TabContent"
contentFrame.Parent = hubFrame

-- Título
local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, 0, 0, 50)
titulo.BackgroundTransparency = 1
titulo.Text = "DraXz Hub V1"
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.TextScaled = true
titulo.Font = Enum.Font.FredokaOne
titulo.Parent = hubFrame

-- UIGradient no Título
local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0.0, Color3.fromRGB(255, 0, 255)),
	ColorSequenceKeypoint.new(0.2, Color3.fromRGB(0, 255, 255)),
	ColorSequenceKeypoint.new(0.4, Color3.fromRGB(0, 255, 127)),
	ColorSequenceKeypoint.new(0.6, Color3.fromRGB(255, 255, 0)),
	ColorSequenceKeypoint.new(0.8, Color3.fromRGB(255, 128, 0)),
	ColorSequenceKeypoint.new(1.0, Color3.fromRGB(255, 0, 0))
})
gradient.Parent = titulo

TweenService:Create(gradient, TweenInfo.new(5, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1), {
	Rotation = 360
}):Play()

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 40, 0, 40)
minimizeBtn.Position = UDim2.new(1, -100, 0, 10)
minimizeBtn.Text = "➖"
minimizeBtn.BackgroundColor3 = Color3.fromRGB(75, 75, 255)
minimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.TextScaled = true
minimizeBtn.Parent = hubFrame

local minimizeCorner = Instance.new("UICorner")
minimizeCorner.CornerRadius = UDim.new(0, 8)
minimizeCorner.Parent = minimizeBtn

minimizeBtn.MouseEnter:Connect(function()
	TweenService:Create(minimizeBtn, TweenInfo.new(0.2), {
		BackgroundColor3 = Color3.fromRGB(0, 0, 255)
	}):Play()
end)
minimizeBtn.MouseLeave:Connect(function()
	TweenService:Create(minimizeBtn, TweenInfo.new(0.2), {
		BackgroundColor3 = Color3.fromRGB(75, 75, 255)
	}):Play()
end)

minimizeBtn.MouseButton1Click:Connect(function()
	if not minimized then
		-- Esconde o conteúdo ANTES de iniciar a animação
		if contentFrame then
			contentFrame.Visible = false
		end
		if tabButtonsFrame then
			tabButtonsFrame.Visible = false
		end
		
		-- Minimizar
		local minimizedSize = UDim2.new(0, 600, 0, 50)
		local minimizedPosition = UDim2.new(0.5, -300, 0, 0)
		
		local tween = TweenService:Create(hubFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
			Size = minimizedSize,
			Position = minimizedPosition
		})
		
		tween:Play()
		minimizeBtn.Text = "🔽"
		minimized = true
	else
		-- Maximizar
		local tween = TweenService:Create(hubFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
			Size = originalSize,
			Position = originalPosition
		})
		
		tween.Completed:Connect(function()
			-- Mostra o conteúdo DEPOIS que a animação terminar
			if contentFrame then
				contentFrame.Visible = true
			end
			if tabButtonsFrame then
				tabButtonsFrame.Visible = true
			end
		end)
		
		tween:Play()
		minimizeBtn.Text = "➖"
		minimized = false
	end
end)

-- Botão Fechar
local fecharBtn = Instance.new("TextButton")
fecharBtn.Size = UDim2.new(0, 40, 0, 40)
fecharBtn.Position = UDim2.new(1, -50, 0, 10)
fecharBtn.Text = "❌"
fecharBtn.BackgroundColor3 = Color3.fromRGB(255, 75, 75)
fecharBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
fecharBtn.Font = Enum.Font.GothamBold
fecharBtn.TextScaled = true
fecharBtn.Parent = hubFrame

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 8)
closeCorner.Parent = fecharBtn

fecharBtn.MouseEnter:Connect(function()
	TweenService:Create(fecharBtn, TweenInfo.new(0.2), {
		BackgroundColor3 = Color3.fromRGB(255, 0, 0)
	}):Play()
end)
fecharBtn.MouseLeave:Connect(function()
	TweenService:Create(fecharBtn, TweenInfo.new(0.2), {
		BackgroundColor3 = Color3.fromRGB(255, 75, 75)
	}):Play()
end)

fecharBtn.MouseButton1Click:Connect(function()
	hubFrame.Visible = false
	minimized = false
end)

-- Abrir/Fechar com H
UserInputService.InputBegan:Connect(function(input, isTyping)
	if not isTyping and input.KeyCode == Enum.KeyCode.H then
		if hubFrame.Visible then
			-- Se estiver visível, esconder
			hubFrame.Visible = false
		else
			-- Se estiver reaberto e minimizado, maximize primeiro
			if minimized then
				-- Restaurar para o tamanho original antes de mostrar
				hubFrame.Size = originalSize
				hubFrame.Position = originalPosition
				
				-- Mostrar o conteúdo
				if contentFrame then
					contentFrame.Visible = true
				end
				if tabButtonsFrame then
					tabButtonsFrame.Visible = true
				end
				
				minimizeBtn.Text = "➖"
				minimized = false
			end
			
			-- Então mostrar o frame
			hubFrame.Visible = true
		end
	end
end)

-- Criar a aba "MAIN"
local tabName = "MAIN"
local tabButton = Instance.new("TextButton")
tabButton.Size = UDim2.new(0, 130, 0, 35)
tabButton.Position = UDim2.new(0, 0, 0, 0)
tabButton.Text = tabName
tabButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
tabButton.TextColor3 = Color3.fromRGB(255, 255, 255)
tabButton.Font = Enum.Font.GothamBold
tabButton.TextScaled = true
tabButton.Parent = tabButtonsFrame

local tabCorner = Instance.new("UICorner")
tabCorner.CornerRadius = UDim.new(0, 6)
tabCorner.Parent = tabButton

local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Size = UDim2.new(1, 0, 1, 0)
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
scrollFrame.ScrollBarThickness = 6
scrollFrame.BackgroundTransparency = 1
scrollFrame.Visible = true
scrollFrame.Name = tabName
scrollFrame.Parent = contentFrame
scrollFrame.ClipsDescendants = true

local layout = Instance.new("UIListLayout")
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 10)
layout.Parent = scrollFrame

local padding = Instance.new("UIPadding")
padding.PaddingTop = UDim.new(0, 10)
padding.PaddingLeft = UDim.new(0, 10)
padding.PaddingRight = UDim.new(0, 10)
padding.Parent = scrollFrame

layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	scrollFrame.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 20)
end)

tabs[tabName] = scrollFrame

tabButton.MouseButton1Click:Connect(function()
	-- Já está visível, então não há necessidade de alternar
	scrollFrame.Visible = true
end)

-- Botão "Kill Aura"
local mainButton = Instance.new("TextButton")
mainButton.Size = UDim2.new(1, -20, 0, 30)
mainButton.Text = "Kill Aura"
mainButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
mainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
mainButton.Font = Enum.Font.Gotham
mainButton.TextSize = 25
mainButton.TextScaled = false
mainButton.AutoButtonColor = true
mainButton.LayoutOrder = 1
mainButton.Parent = tabs["MAIN"]

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 6)
btnCorner.Parent = mainButton

mainButton.MouseButton1Click:Connect(function()
	while wait(0.1) do
		for _, plr in pairs(game.Players:GetChildren()) do
			if plr.Name ~= player.Name then
				for i = 30, 20, -99 do
					game.ReplicatedStorage.meleeEvent:FireServer(plr)
				end
			end
		end
	end
end)

-- Botão "Refresh"
local refreshButton = Instance.new("TextButton")
refreshButton.Size = UDim2.new(1, -20, 0, 30)
refreshButton.Text = "Remover Lag"
refreshButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
refreshButton.TextColor3 = Color3.fromRGB(255, 255, 255)
refreshButton.Font = Enum.Font.Gotham
refreshButton.TextSize = 25
refreshButton.TextScaled = false
refreshButton.AutoButtonColor = true
refreshButton.LayoutOrder = 2
refreshButton.Parent = tabs["MAIN"]

local refreshBtnCorner = Instance.new("UICorner")
refreshBtnCorner.CornerRadius = UDim.new(0, 6)
refreshBtnCorner.Parent = refreshButton

refreshButton.MouseButton1Click:Connect(function()
	local A_1 = "\66\114\111\121\111\117\98\97\100\100"
	local Event = game:GetService("Workspace").Remote.loadchar
	Event:InvokeServer(A_1)
end)

-- Botão "God"
local godButton = Instance.new("TextButton")
godButton.Size = UDim2.new(1, -20, 0, 30)
godButton.Text = "God"
godButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
godButton.TextColor3 = Color3.fromRGB(255, 255, 255)
godButton.Font = Enum.Font.Gotham
godButton.TextSize = 25
godButton.TextScaled = false
godButton.AutoButtonColor = true
godButton.LayoutOrder = 3
godButton.Parent = tabs["MAIN"]

local godBtnCorner = Instance.new("UICorner")
godBtnCorner.CornerRadius = UDim.new(0, 6)
godBtnCorner.Parent = godButton

godButton.MouseButton1Click:Connect(function()
	local player = game:GetService("Players").LocalPlayer
	local Player = game.Players.LocalPlayer
	local Character = Player.Character
	local Humanoid = Character.Humanoid

	Humanoid.MaxHealth = 999999999999999999999
	Humanoid.Health = Humanoid.MaxHealth
end)

-- Botão "Max Health"
local maxHealthButton = Instance.new("TextButton")
maxHealthButton.Size = UDim2.new(1, -20, 0, 30)
maxHealthButton.Text = "Max Health"
maxHealthButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
maxHealthButton.TextColor3 = Color3.fromRGB(255, 255, 255)
maxHealthButton.Font = Enum.Font.Gotham
maxHealthButton.TextSize = 25
maxHealthButton.TextScaled = false
maxHealthButton.AutoButtonColor = true
maxHealthButton.LayoutOrder = 4
maxHealthButton.Parent = tabs["MAIN"]

local maxHealthBtnCorner = Instance.new("UICorner")
maxHealthBtnCorner.CornerRadius = UDim.new(0, 6)
maxHealthBtnCorner.Parent = maxHealthButton

maxHealthButton.MouseButton1Click:Connect(function()
	local Player = game.Players.LocalPlayer
	local Character = Player.Character
	local Humanoid = Character.Humanoid

	Humanoid.MaxHealth = 9999
	Humanoid.Health = Humanoid.MaxHealth
end)

-- Botão "Cozinha"
local cozinhaButton = Instance.new("TextButton")
cozinhaButton.Size = UDim2.new(1, -20, 0, 30)
cozinhaButton.Text = "Sala"
cozinhaButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
cozinhaButton.TextColor3 = Color3.fromRGB(255, 255, 255)
cozinhaButton.Font = Enum.Font.Gotham
cozinhaButton.TextSize = 25
cozinhaButton.TextScaled = false
cozinhaButton.AutoButtonColor = true
cozinhaButton.LayoutOrder = 5
cozinhaButton.Parent = tabs["MAIN"]

local cozinhaCorner = Instance.new("UICorner")
cozinhaCorner.CornerRadius = UDim.new(0, 6)
cozinhaCorner.Parent = cozinhaButton

cozinhaButton.MouseButton1Click:Connect(function()
	local char = player.Character
	if char and char:FindFirstChild("HumanoidRootPart") then
		char:MoveTo(Vector3.new(916, 97, 2400))
	end
end)

-- Botão "Policia Sala"
local policiaButton = Instance.new("TextButton")
policiaButton.Size = UDim2.new(1, -20, 0, 30)
policiaButton.Text = "Policia Sala"
policiaButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
policiaButton.TextColor3 = Color3.fromRGB(255, 255, 255)
policiaButton.Font = Enum.Font.Gotham
policiaButton.TextSize = 25
policiaButton.TextScaled = false
policiaButton.AutoButtonColor = true
policiaButton.LayoutOrder = 6
policiaButton.Parent = tabs["MAIN"]

local policiaCorner = Instance.new("UICorner")
policiaCorner.CornerRadius = UDim.new(0, 6)
policiaCorner.Parent = policiaButton

policiaButton.MouseButton1Click:Connect(function()
	local char = player.Character
	if char and char:FindFirstChild("HumanoidRootPart") then
		char:MoveTo(Vector3.new(823, 99, 2257))
	end
end)

-- Botão "Criminal Base"
local criminalButton = Instance.new("TextButton")
criminalButton.Size = UDim2.new(1, -20, 0, 30)
criminalButton.Text = "Criminal Base"
criminalButton.BackgroundColor3 = Color3.fromRGB(109, 110, 108)
criminalButton.TextColor3 = Color3.fromRGB(255, 255, 255)
criminalButton.Font = Enum.Font.Gotham
criminalButton.TextSize = 25
criminalButton.TextScaled = false
criminalButton.AutoButtonColor = true
criminalButton.LayoutOrder = 7
criminalButton.Parent = tabs["MAIN"]

local criminalCorner = Instance.new("UICorner")
criminalCorner.CornerRadius = UDim.new(0, 6)
criminalCorner.Parent = criminalButton

criminalButton.MouseButton1Click:Connect(function()
	local char = player.Character
	if char and char:FindFirstChild("HumanoidRootPart") then
		char:MoveTo(Vector3.new(-956, 91, 2072))
	end
end)
