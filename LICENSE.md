-- Configurações principais
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

-- Configurações ajustadas para PCX DO TG
local Settings = {
    Aimbot = {
        Enabled = true,
        Key = Enum.UserInputType.MouseButton2,
        Smoothness = 0.3,
        FOV = 120,
        TargetPart = "Head",
        VisibleCheck = true,
        TeamCheck = true
    },
    ESP = {
        Enabled = true,
        Box = true,
        TeamColor = Color3.fromRGB(0, 255, 0),
        EnemyColor = Color3.fromRGB(255, 50, 50)
    },
    NoRecoil = {
        Enabled = true,
        Power = 0.8
    },
    GUI = {
        Key = Enum.KeyCode.F,
        Visible = false,
        Color = Color3.fromRGB(30, 60, 120)  -- Cor azul temática
    }
}

-- Variáveis globais
local ESPObjects = {}
local GUI

-- Função para criar a GUI otimizada
local function CreatePCXGUI()
    -- Remover GUI existente
    if game:GetService("CoreGui"):FindFirstChild("PCX_GUI") then
        game:GetService("CoreGui").PCX_GUI:Destroy()
    end

    -- Criar elementos principais
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "PCX_GUI"
    ScreenGui.Parent = game:GetService("CoreGui")
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.ResetOnSpawn = false

    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 280, 0, 0)  -- Altura inicial zero para animação
    MainFrame.Position = UDim2.new(0.5, -140, 0.3, 0)
    MainFrame.BackgroundColor3 = Settings.GUI.Color
    MainFrame.BackgroundTransparency = 0.15
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Visible = false
    MainFrame.Parent = ScreenGui

    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = MainFrame

    -- Cabeçalho
    local Header = Instance.new("Frame")
    Header.Size = UDim2.new(1, 0, 0, 40)
    Header.Position = UDim2.new(0, 0, 0, 0)
    Header.BackgroundColor3 = Color3.fromRGB(20, 40, 80)
    Header.BackgroundTransparency = 0.3
    Header.Parent = MainFrame

    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 8)
    HeaderCorner.Parent = Header

    local Title = Instance.new("TextLabel")
    Title.Text = "PCX DO TG - MENU"
    Title.Size = UDim2.new(1, -10, 1, 0)
    Title.Position = UDim2.new(0, 10, 0, 0)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 16
    Title.TextColor3 = Color3.new(1, 1, 1)
    Title.BackgroundTransparency = 1
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header

    local KeyLabel = Instance.new("TextLabel")
    KeyLabel.Text = "[F]"
    KeyLabel.Size = UDim2.new(0, 30, 1, 0)
    KeyLabel.Position = UDim2.new(1, -30, 0, 0)
    KeyLabel.Font = Enum.Font.GothamBold
    KeyLabel.TextSize = 14
    KeyLabel.TextColor3 = Color3.new(1, 1, 1)
    KeyLabel.BackgroundTransparency = 1
    KeyLabel.Parent = Header

    -- Função para criar botões de toggle
    local function CreateToggle(text, yPos, config, key)
        local ToggleFrame = Instance.new("Frame")
        ToggleFrame.Size = UDim2.new(0.9, 0, 0, 30)
        ToggleFrame.Position = UDim2.new(0.05, 0, yPos, 0)
        ToggleFrame.BackgroundTransparency = 1
        ToggleFrame.Parent = MainFrame

        local Label = Instance.new("TextLabel")
        Label.Text = text
        Label.Size = UDim2.new(0.7, 0, 1, 0)
        Label.TextXAlignment = Enum.TextXAlignment.Left
        Label.Font = Enum.Font.Gotham
        Label.TextSize = 14
        Label.TextColor3 = Color3.new(1, 1, 1)
        Label.BackgroundTransparency = 1
        Label.Parent = ToggleFrame

        local Toggle = Instance.new("TextButton")
        Toggle.Size = UDim2.new(0.25, 0, 0.8, 0)
        Toggle.Position = UDim2.new(0.75, 0, 0.1, 0)
        Toggle.Text = config[key] and "ON" or "OFF"
        Toggle.TextColor3 = Color3.new(1, 1, 1)
        Toggle.BackgroundColor3 = config[key] and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(180, 0, 0)
        Toggle.Font = Enum.Font.GothamBold
        Toggle.TextSize = 14
        Toggle.Parent = ToggleFrame

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 4)
        Corner.Parent = Toggle

        Toggle.MouseButton1Click:Connect(function()
            config[key] = not config[key]
            Toggle.Text = config[key] and "ON" or "OFF"
            Toggle.BackgroundColor3 = config[key] and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(180, 0, 0)
        end)

        return ToggleFrame
    end

    -- Criar toggles
    local yPos = 0.15
    local toggles = {
        {"Aimbot", Settings.Aimbot, "Enabled"},
        {"Mira na Cabeça", Settings.Aimbot, "TargetPart"},
        {"Ver Time", Settings.Aimbot, "TeamCheck"},
        {"No Recoil", Settings.NoRecoil, "Enabled"},
        {"ESP Box", Settings.ESP, "Enabled"}
    }

    for _, toggle in ipairs(toggles) do
        CreateToggle(toggle[1], yPos, toggle[2], toggle[3])
        yPos = yPos + 0.1
    end

    -- Slider de Suavidade
    local SliderFrame = Instance.new("Frame")
    SliderFrame.Size = UDim2.new(0.9, 0, 0, 50)
    SliderFrame.Position = UDim2.new(0.05, 0, yPos + 0.05, 0)
    SliderFrame.BackgroundTransparency = 1
    SliderFrame.Parent = MainFrame

    local SliderLabel = Instance.new("TextLabel")
    SliderLabel.Text = "Suavidade: " .. string.format("%.1f", Settings.Aimbot.Smoothness * 10)
    SliderLabel.Size = UDim2.new(1, 0, 0, 20)
    SliderLabel.Font = Enum.Font.Gotham
    SliderLabel.TextSize = 14
    SliderLabel.TextColor3 = Color3.new(1, 1, 1)
    SliderLabel.BackgroundTransparency = 1
    SliderLabel.Parent = SliderFrame

    local Slider = Instance.new("Frame")
    Slider.Size = UDim2.new(1, 0, 0, 6)
    Slider.Position = UDim2.new(0, 0, 0.5, 0)
    Slider.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    Slider.Parent = SliderFrame

    local SliderCorner = Instance.new("UICorner")
    SliderCorner.Parent = Slider

    local Fill = Instance.new("Frame")
    Fill.Size = UDim2.new(Settings.Aimbot.Smoothness, 0, 1, 0)
    Fill.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
    Fill.Parent = Slider

    local FillCorner = Instance.new("UICorner")
    FillCorner.Parent = Fill

    local SliderButton = Instance.new("TextButton")
    SliderButton.Size = UDim2.new(0, 20, 0, 20)
    SliderButton.Position = UDim2.new(Settings.Aimbot.Smoothness, -10, 0.5, -10)
    SliderButton.Text = ""
    SliderButton.BackgroundColor3 = Color3.new(1, 1, 1)
    SliderButton.Parent = SliderFrame

    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(1, 0)
    ButtonCorner.Parent = SliderButton

    -- Lógica do Slider
    local function UpdateSlider(value)
        value = math.clamp(value, 0.1, 1)
        Settings.Aimbot.Smoothness = value
        Fill.Size = UDim2.new(value, 0, 1, 0)
        SliderButton.Position = UDim2.new(value, -10, 0.5, -10)
        SliderLabel.Text = "Suavidade: " .. string.format("%.1f", value * 10)
    end

    local dragging
    SliderButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local posX = (input.Position.X - Slider.AbsolutePosition.X) / Slider.AbsoluteSize.X
            UpdateSlider(posX)
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    -- Definir altura final após criar todos os elementos
    MainFrame.Size = UDim2.new(0, 280, 0, (yPos + 0.2) * 100)

    return ScreenGui
end

-- Função para alternar a GUI com animação
local function ToggleGUI()
    Settings.GUI.Visible = not Settings.GUI.Visible
    
    if Settings.GUI.Visible then
        GUI.MainFrame.Visible = true
        GUI.MainFrame.Size = UDim2.new(0, 280, 0, 0)
        local tween = TweenService:Create(
            GUI.MainFrame,
            TweenInfo.new(0.3, Enum.EasingStyle.Quad),
            {Size = UDim2.new(0, 280, 0, 220)}
        )
        tween:Play()
    else
        local tween = TweenService:Create(
            GUI.MainFrame,
            TweenInfo.new(0.3, Enum.EasingStyle.Quad),
            {Size = UDim2.new(0, 280, 0, 0)}
        )
        tween:Play()
        tween.Completed:Wait()
        GUI.MainFrame.Visible = false
    end
end

-- Inicializar GUI
GUI = CreatePCXGUI()

-- Tecla F para abrir/fechar
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Settings.GUI.Key and not gameProcessed then
        ToggleGUI()
    end
end)

-- [RESTANTE DO SEU SCRIPT AQUI...]
