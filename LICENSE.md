-- ✅ GUI FUNCIONAL com tecla F
local function CreateFixedGUI()
    -- Destruir GUI antiga se existir
    if game:GetService("CoreGui"):FindFirstChild("AimTrainerGUI") then
        game:GetService("CoreGui").AimTrainerGUI:Destroy()
    end

    -- Criar elementos da GUI
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "AimTrainerGUI"
    ScreenGui.Parent = game:GetService("CoreGui")
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.ResetOnSpawn = false

    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 300, 0, 280)
    MainFrame.Position = UDim2.new(0.5, -150, 0.5, -140)
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    MainFrame.BackgroundTransparency = 0.1
    MainFrame.BorderSizePixel = 0
    MainFrame.Visible = Settings.GUI.Visible
    MainFrame.Parent = ScreenGui

    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = MainFrame

    -- Título
    local Title = Instance.new("TextLabel")
    Title.Text = "PCX TRAINER (F)"
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.Position = UDim2.new(0, 0, 0, 0)
    Title.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    Title.BackgroundTransparency = 0.2
    Title.TextColor3 = Color3.new(1, 1, 1)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18
    Title.Parent = MainFrame

    local TitleCorner = Instance.new("UICorner")
    TitleCorner.CornerRadius = UDim.new(0, 8)
    TitleCorner.Parent = Title

    -- Função para criar botões de toggle
    local yOffset = 0.15
    local function CreateToggleOption(text, configTable, configKey)
        local ToggleFrame = Instance.new("Frame")
        ToggleFrame.Size = UDim2.new(0.9, 0, 0, 30)
        ToggleFrame.Position = UDim2.new(0.05, 0, yOffset, 0)
        ToggleFrame.BackgroundTransparency = 1
        ToggleFrame.Parent = MainFrame

        local OptionText = Instance.new("TextLabel")
        OptionText.Text = text
        OptionText.Size = UDim2.new(0.7, 0, 1, 0)
        OptionText.TextXAlignment = Enum.TextXAlignment.Left
        OptionText.TextColor3 = Color3.new(1, 1, 1)
        OptionText.Font = Enum.Font.Gotham
        OptionText.TextSize = 14
        OptionText.BackgroundTransparency = 1
        OptionText.Parent = ToggleFrame

        local ToggleButton = Instance.new("TextButton")
        ToggleButton.Size = UDim2.new(0.25, 0, 0.8, 0)
        ToggleButton.Position = UDim2.new(0.75, 0, 0.1, 0)
        ToggleButton.Text = configTable[configKey] and "ON" or "OFF"
        ToggleButton.TextColor3 = Color3.new(1, 1, 1)
        ToggleButton.Font = Enum.Font.GothamBold
        ToggleButton.TextSize = 14
        ToggleButton.BackgroundColor3 = configTable[configKey] and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(170, 0, 0)
        ToggleButton.Parent = ToggleFrame

        local Corner = Instance.new("UICorner")
        Corner.CornerRadius = UDim.new(0, 4)
        Corner.Parent = ToggleButton

        ToggleButton.MouseButton1Click:Connect(function()
            configTable[configKey] = not configTable[configKey]
            ToggleButton.Text = configTable[configKey] and "ON" or "OFF"
            ToggleButton.BackgroundColor3 = configTable[configKey] and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(170, 0, 0)
        end)

        yOffset = yOffset + 0.1
        return ToggleFrame
    end

    -- Criar opções
    CreateToggleOption("Aimbot ao Atirar", Settings.Aimbot, "ActiveOnFire")
    CreateToggleOption("Verificar Time", Settings.Aimbot, "TeamCheck")
    CreateToggleOption("Verificar Visão", Settings.Aimbot, "VisibleCheck")
    CreateToggleOption("No Recoil", Settings.NoRecoil, "Stabilize")

    -- Slider de Suavidade
    local SliderFrame = Instance.new("Frame")
    SliderFrame.Size = UDim2.new(0.9, 0, 0, 50)
    SliderFrame.Position = UDim2.new(0.05, 0, yOffset + 0.05, 0)
    SliderFrame.BackgroundTransparency = 1
    SliderFrame.Parent = MainFrame

    local SliderLabel = Instance.new("TextLabel")
    SliderLabel.Text = "Suavidade: " .. string.format("%.2f", Settings.Aimbot.Smoothness)
    SliderLabel.Size = UDim2.new(1, 0, 0, 20)
    SliderLabel.Font = Enum.Font.Gotham
    SliderLabel.TextSize = 14
    SliderLabel.TextColor3 = Color3.new(1, 1, 1)
    SliderLabel.BackgroundTransparency = 1
    SliderLabel.Parent = SliderFrame

    local SliderBar = Instance.new("Frame")
    SliderBar.Size = UDim2.new(1, 0, 0, 6)
    SliderBar.Position = UDim2.new(0, 0, 0.5, 0)
    SliderBar.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    SliderBar.Parent = SliderFrame

    local SliderCorner = Instance.new("UICorner")
    SliderCorner.Parent = SliderBar

    local SliderFill = Instance.new("Frame")
    SliderFill.Size = UDim2.new(Settings.Aimbot.Smoothness, 0, 1, 0)
    SliderFill.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
    SliderFill.Parent = SliderBar

    local FillCorner = Instance.new("UICorner")
    FillCorner.Parent = SliderFill

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
        value = math.clamp(value, 0, 1)
        Settings.Aimbot.Smoothness = value
        SliderFill.Size = UDim2.new(value, 0, 1, 0)
        SliderButton.Position = UDim2.new(value, -10, 0.5, -10)
        SliderLabel.Text = "Suavidade: " .. string.format("%.2f", value)
    end

    local dragging
    local function InputChanged(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local posX = (input.Position.X - SliderBar.AbsolutePosition.X) / SliderBar.AbsoluteSize.X
            UpdateSlider(posX)
        end
    end

    SliderButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            input.Changed:Connect(InputChanged)
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    return ScreenGui
end

-- Atualizar o GUI existente
GUI = CreateFixedGUI()

-- Função para alternar a GUI
local function ToggleGUI()
    Settings.GUI.Visible = not Settings.GUI.Visible
    GUI.MainFrame.Visible = Settings.GUI.Visible
    
    -- Efeito de animação
    if Settings.GUI.Visible then
        GUI.MainFrame.Size = UDim2.new(0, 300, 0, 0)
        GUI.MainFrame.Visible = true
        local tween = TweenService:Create(
            GUI.MainFrame,
            TweenInfo.new(0.3, Enum.EasingStyle.Quad),
            {Size = UDim2.new(0, 300, 0, 280)}
        )
        tween:Play()
    else
        local tween = TweenService:Create(
            GUI.MainFrame,
            TweenInfo.new(0.3, Enum.EasingStyle.Quad),
            {Size = UDim2.new(0, 300, 0, 0)}
        )
        tween:Play()
        tween.Completed:Wait()
        GUI.MainFrame.Visible = false
    end
end

-- Tecla F para abrir/fechar
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Settings.GUI.Key and not gameProcessed then
        ToggleGUI()
    end
end)
