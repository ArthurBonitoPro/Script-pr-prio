-- Configurações SCRIPT

-- Gui to Lua (VIP VERSION)
-- Version: 6.9

-- Instances:

local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local Frame_2 = Instance.new("Frame")
local TextLabel = Instance.new("TextLabel")
local TextButton = Instance.new("TextButton")
local UICorner = Instance.new("UICorner")
local UICorner_2 = Instance.new("UICorner")
local UICorner_3 = Instance.new("UICorner")

--Properties:

ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.ResetOnSpawn = false
print("ESP Admin Carregado")

Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(34, 34, 34)
Frame.BorderColor3 = Color3.fromRGB(0, 0, 0)
Frame.BorderSizePixel = 0
Frame.Position = UDim2.new(0.4, 0, 0.4, 0)
Frame.Size = UDim2.new(0, 150, 0, 100)

UICorner.Parent = Frame
UICorner.CornerRadius = UDim.new(0, 8)

Frame_2.Parent = Frame
Frame_2.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
Frame_2.BorderColor3 = Color3.fromRGB(0, 0, 0)
Frame_2.BorderSizePixel = 0
Frame_2.Size = UDim2.new(0, 150, 0, 25)

UICorner_2.Parent = Frame_2
UICorner_2.CornerRadius = UDim.new(0, 8)

TextLabel.Parent = Frame_2
TextLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextLabel.BackgroundTransparency = 1.000
TextLabel.BorderColor3 = Color3.fromRGB(0, 0, 0)
TextLabel.BorderSizePixel = 0
TextLabel.Position = UDim2.new(0.1, 0, 0, 0)
TextLabel.Size = UDim2.new(0, 120, 0, 25)
TextLabel.Font = Enum.Font.Sarpanch
TextLabel.Text = "ESP Admin"
TextLabel.TextColor3 = Color3.fromRGB(0, 150, 255) -- Azul claro
TextLabel.TextSize = 18.000

TextButton.Parent = Frame
TextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
TextButton.BorderSizePixel = 0
TextButton.Position = UDim2.new(0.1, 0, 0.4, 0)
TextButton.Size = UDim2.new(0, 120, 0, 40)
TextButton.Font = Enum.Font.SourceSansItalic
TextButton.Text = "OFF"
TextButton.TextColor3 = Color3.fromRGB(0, 0, 0)
TextButton.TextSize = 20.000

UICorner_3.Parent = TextButton
UICorner_3.CornerRadius = UDim.new(0, 8)

-- Scripts:

local function ESPScript() -- TextButton.LocalScript
    local script = Instance.new('LocalScript', TextButton)

    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    local isESPActive = false

    local function createHighlight(player)
        if player == LocalPlayer then return end -- Ignora o próprio jogador
        local character = player.Character or player.CharacterAdded:Wait()
        
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.FillColor = Color3.fromRGB(0, 100, 255) -- Azul brilhante
        highlight.OutlineColor = Color3.fromRGB(0, 200, 255) -- Azul neon
        highlight.FillTransparency = 0.3
        highlight.OutlineTransparency = 0
        highlight.Parent = character
    end

    local function removeHighlight(player)
        local character = player.Character
        if character then
            for _, child in pairs(character:GetChildren()) do
                if child.Name == "ESP_Highlight" then
                    child:Destroy()
                end
            end
        end
    end

    TextButton.MouseButton1Click:Connect(function()
        isESPActive = not isESPActive
        TextButton.Text = isESPActive and "ON" or "OFF"

        if isESPActive then
            -- Ativa o ESP para todos os jogadores
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    createHighlight(player)
                end
            end
        else
            -- Desativa o ESP
            for _, player in pairs(Players:GetPlayers()) do
                removeHighlight(player)
            end
        end
    end)

    -- Atualiza o ESP quando um jogador entra/sai
    Players.PlayerAdded:Connect(function(player)
        if isESPActive then
            player.CharacterAdded:Connect(function()
                createHighlight(player)
            end)
        end
    end)

    Players.PlayerRemoving:Connect(function(player)
        removeHighlight(player)
    end)
end
coroutine.wrap(ESPScript)()

local function DragScript() -- Frame.LocalScript
    local script = Instance.new('LocalScript', Frame)

    local UserInputService = game:GetService("UserInputService")
    local dragging
    local dragInput
    local dragStart
    local startPos

    local function update(input)
        local delta = input.Position - dragStart
        Frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    Frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = Frame.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    Frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)
end
coroutine.wrap(DragScript)()
