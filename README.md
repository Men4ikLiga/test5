local screenGui = Instance.new("ScreenGui")
local mainFrame = Instance.new("Frame")
local title = Instance.new("TextLabel")
local buttonsFrame = Instance.new("Frame")
local uiListLayout = Instance.new("UIListLayout")
local closeButton = Instance.new("TextButton")

-- Настройка GUI
screenGui.Name = "AdvancedMenu"
screenGui.ResetOnSpawn = false
screenGui.Enabled = false  -- Изначально выключено

mainFrame.Size = UDim2.new(0, 250, 0, 300)
mainFrame.Position = UDim2.new(0, 100, 0, 100)
mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = mainFrame

-- Заголовок
title.Size = UDim2.new(1, 0, 0, 30)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
title.Text = "Advanced Menu (Insert: OFF)"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 8)
titleCorner.Parent = title

-- Фрейм для кнопок
buttonsFrame.Size = UDim2.new(1, -20, 1, -60)
buttonsFrame.Position = UDim2.new(0, 10, 0, 40)
buttonsFrame.BackgroundTransparency = 1
buttonsFrame.Parent = mainFrame

uiListLayout.Padding = UDim.new(0, 5)
uiListLayout.Parent = buttonsFrame

-- Кнопка закрытия
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -30, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 14
closeButton.Parent = mainFrame

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 12)
closeCorner.Parent = closeButton

-- Таблица для хранения кнопок
local buttonFunctions = {}
local menuEnabled = false

-- Функция создания кнопки
function createButton(buttonName, buttonCode)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, 0, 0, 35)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.Text = buttonName
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Font = Enum.Font.Gotham
    button.TextSize = 14
    button.Parent = buttonsFrame
    
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 6)
    buttonCorner.Parent = button
    
    buttonFunctions[buttonName] = buttonCode
    
    button.MouseButton1Click:Connect(function()
        if menuEnabled and buttonFunctions[buttonName] then
            buttonFunctions[buttonName]()
        end
    end)
    
    return button
end

-- Функция переключения меню
local function toggleMenu()
    menuEnabled = not menuEnabled
    screenGui.Enabled = menuEnabled
    
    if menuEnabled then
        title.Text = "Advanced Menu (Insert: ON)"
        mainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    else
        title.Text = "Advanced Menu (Insert: OFF)" 
        mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    end
end

-- Обработка клавиши Insert
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.Insert then
        toggleMenu()
    end
end)

-- Закрытие меню
closeButton.MouseButton1Click:Connect(function()
    screenGui.Enabled = false
    menuEnabled = false
    title.Text = "Advanced Menu (Insert: OFF)"
end)

-- ██████████████████████████████████████████████████████████████████████████
-- ███ ФУНКЦИИ МЕНЮ (ДОБАВЛЯЙ СЮДА СВОИ ФУНКЦИИ)
-- ██████████████████████████████████████████████████████████████████████████

-- Функция ESP (добавь в начало скрипта)
local function CreateESP()
    local Players = game:GetService("Players")
    local highlights = {}
    
    local function createHighlight(player)
        local character = player.Character
        if not character then return end
        
        local highlight = Instance.new("Highlight")
        highlight.Name = player.Name .. "_ESP"
        highlight.Parent = character
        highlight.FillColor = Color3.fromRGB(255, 0, 0)
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.7
        highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
        
        highlights[player] = highlight
        
        player.CharacterAdded:Connect(function(newCharacter)
            if highlights[player] then
                highlights[player]:Destroy()
                highlights[player] = nil
            end
            
            wait(1)
            
            local newHighlight = Instance.new("Highlight")
            newHighlight.Name = player.Name .. "_ESP"
            newHighlight.Parent = newCharacter
            newHighlight.FillColor = Color3.fromRGB(255, 0, 0)
            newHighlight.OutlineColor = Color3.fromRGB(255, 255, 255)
            newHighlight.FillTransparency = 0.7
            newHighlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            
            highlights[player] = newHighlight
        end)
        
        player.CharacterRemoving:Connect(function()
            if highlights[player] then
                highlights[player]:Destroy()
                highlights[player] = nil
            end
        end)
    end

    -- Подсвечиваем всех игроков
    for _, player in ipairs(Players:GetPlayers()) do
        createHighlight(player)
    end
    
    Players.PlayerAdded:Connect(function(player)
        createHighlight(player)
    end)
    
    Players.PlayerRemoving:Connect(function(player)
        if highlights[player] then
            highlights[player]:Destroy()
            highlights[player] = nil
        end
    end)
    
    return {
        Disable = function()
            for player, highlight in pairs(highlights) do
                if highlight then
                    highlight:Destroy()
                end
            end
            highlights = {}
        end
    }
end

-- Переменная для хранения ESP
local espInstance = nil

-- Создаем кнопки по умолчанию
-- Кнопка ESP (включение/выключение)
createButton("ESP Players", function()
    if espInstance == nil then
        -- Включаем ESP
        espInstance = CreateESP()
        print("ESP включен!")
    else
        -- Выключаем ESP
        espInstance:Disable()
        espInstance = nil
        print("ESP выключен!")
    end
end)

-- Полет
createButton("Flight", function()
    local player = game.Players.LocalPlayer
    local character = player.CharacterAdded:Wait()
    if not character then return end
    
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then return end
    
    -- Проверяем, активирован ли уже полет
    if character:FindFirstChild("FlightBodyVelocity") then
        print("Полет уже активирован!")
        return
    end
    
    -- Создаем части для полета
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Name = "FlightBodyVelocity"
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
    
    local bodyGyro = Instance.new("BodyGyro")
    bodyGyro.Name = "FlightBodyGyro"
    bodyGyro.MaxTorque = Vector3.new(0, 0, 0)
    bodyGyro.P = 1000
    bodyGyro.D = 50
    
    -- Устанавливаем родителя
    bodyVelocity.Parent = character:FindFirstChild("HumanoidRootPart") or character.PrimaryPart
    bodyGyro.Parent = character:FindFirstChild("HumanoidRootPart") or character.PrimaryPart
    
    -- Включаем полет
    bodyVelocity.MaxForce = Vector3.new(0, 40000, 0)
    bodyGyro.MaxTorque = Vector3.new(40000, 0, 40000)
    
    -- Функция полета
    local userInputService = game:GetService("UserInputService")
    local flightSpeed = 50
    
    local function onInput(input, gameProcessed)
        if gameProcessed then return end
        
        local rootPart = character:FindFirstChild("HumanoidRootPart") or character.PrimaryPart
        if not rootPart then return end
        
        if input.UserInputType == Enum.UserInputType.Keyboard then
            local direction = Vector3.new(0, 0, 0)
            
            if input.KeyCode == Enum.KeyCode.W then
                direction = direction + rootPart.CFrame.LookVector
            end
            if input.KeyCode == Enum.KeyCode.S then
                direction = direction - rootPart.CFrame.LookVector
            end
            if input.KeyCode == Enum.KeyCode.A then
                direction = direction - rootPart.CFrame.RightVector
            end
            if input.KeyCode == Enum.KeyCode.D then
                direction = direction + rootPart.CFrame.RightVector
            end
            if input.KeyCode == Enum.KeyCode.Space then
                direction = direction + Vector3.new(0, 1, 0)
            end
            if input.KeyCode == Enum.KeyCode.LeftControl then
                direction = direction + Vector3.new(0, -1, 0)
            end
            
            bodyVelocity.Velocity = direction * flightSpeed
        end
    end
    
    -- Обработчики ввода
    userInputService.InputBegan:Connect(onInput)
    userInputService.InputEnded:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        
        if input.UserInputType == Enum.UserInputType.Keyboard then
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        end
    end)
    
    -- Очистка при смерти
    humanoid.Died:Connect(function()
        bodyVelocity:Destroy()
        bodyGyro:Destroy()
    end)
    
    print("Полет активирован! Управление: WASD, Space(вверх), Ctrl(вниз)")
end)

createButton("Teleport Up", function()
    local player = game.Players.LocalPlayer
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.Position += Vector3.new(0, 10, 0)
    end
end)

createButton("Print Hello", function()
end)

-- Добавляем меню в интерфейс
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- API для внешнего использования
local menuAPI = {}

function menuAPI.addButton(name, code)
    createButton(name, code)
end

function menuAPI.toggle()
    toggleMenu()
end

function menuAPI.isEnabled()
    return menuEnabled
end

function menuAPI.destroy()
    screenGui:Destroy()
end

return menuAPI
