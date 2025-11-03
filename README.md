local Players = game:GetService("Players")
local playerHighlights = {}
local enabled = true

-- Показ сообщения на 3 секунды
local function showMessage()
    local messageGui = Instance.new("ScreenGui")
    messageGui.Name = "TelegramMessage"
    messageGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")
    
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 300, 0, 60)
    frame.Position = UDim2.new(0.5, -150, 0.5, -30)
    frame.BackgroundColor3 = Color3.new(0, 0, 0)
    frame.BackgroundTransparency = 0.3
    frame.BorderSizePixel = 0
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = frame
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = "https://t.me/SpiderRobloxScripts"
    label.TextColor3 = Color3.new(1, 1, 1)
    label.TextSize = 16
    label.Font = Enum.Font.GothamBold
    label.Parent = frame
    
    frame.Parent = messageGui
    
    -- Удаляем через 3 секунды
    wait(3)
    messageGui:Destroy()
end

-- Показываем сообщение при запуске
showMessage()

-- Функция создания подсветки
local function createHighlight(character, player)
	if not enabled then return end
	if not character:FindFirstChild("HumanoidRootPart") then
		character:WaitForChild("HumanoidRootPart")
	end
	
	local highlight = Instance.new("Highlight")
	highlight.Name = "PlayerHighlight"
	highlight.Adornee = character
	highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	highlight.FillTransparency = 0.3
	
	-- Красный для себя, желтый для других
	if player == Players.LocalPlayer then
		highlight.FillColor = Color3.new(1, 0, 0) -- Красный
	else
		highlight.FillColor = Color3.new(1, 1, 0) -- Желтый
	end
	
	highlight.OutlineColor = Color3.new(1, 1, 1)
	
	if playerHighlights[player] then
		playerHighlights[player]:Destroy()
	end
	
	highlight.Parent = character
	playerHighlights[player] = highlight
end

-- Функция удаления подсветки
local function removeHighlight(player)
	if playerHighlights[player] then
		playerHighlights[player]:Destroy()
		playerHighlights[player] = nil
	end
end

-- Обработка игроков
local function setupPlayer(player)
	player.CharacterAdded:Connect(function(character)
		createHighlight(character, player)
	end)
	
	if player.Character then
		createHighlight(player.Character, player)
	end
end

-- Запуск для всех игроков
for _, player in ipairs(Players:GetPlayers()) do
	setupPlayer(player)
end

Players.PlayerAdded:Connect(setupPlayer)
Players.PlayerRemoving:Connect(removeHighlight)

-- Простое GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "HighlightGUI"
screenGui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")

local toggle = Instance.new("TextButton")
toggle.Size = UDim2.new(0, 100, 0, 40)
toggle.Position = UDim2.new(0, 10, 0, 10)
toggle.BackgroundColor3 = Color3.new(0, 0.5, 0)
toggle.Text = "ON"
toggle.TextColor3 = Color3.new(1, 1, 1)
toggle.TextSize = 14
toggle.Font = Enum.Font.GothamBold
toggle.Parent = screenGui

toggle.MouseButton1Click:Connect(function()
	enabled = not enabled
	toggle.BackgroundColor3 = enabled and Color3.new(0, 0.5, 0) or Color3.new(0.5, 0, 0)
	toggle.Text = enabled and "ON" or "OFF"
	
	if not enabled then
		for player in pairs(playerHighlights) do
			removeHighlight(player)
		end
	else
		for _, player in ipairs(Players:GetPlayers()) do
			if player.Character then
				createHighlight(player.Character, player)
			end
		end
	end
end)

print("🎯 Подсветка запущена! Красный - ты, Желтый - другие")
