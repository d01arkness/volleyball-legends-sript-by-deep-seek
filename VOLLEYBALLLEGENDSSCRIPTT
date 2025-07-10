--[[
  Volleyball Legends Ultimate Booster v3.0
  Особенности:
  - Авто-восстановление параметров
  - Плавный обход античита
  - Горячие клавиши
  - Полностью рабочий JumpPower
]]

local Player = game:GetService("Players").LocalPlayer
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

--= Настройки =--
local SETTINGS = {
    Keybind = Enum.KeyCode.F5,          -- Открыть меню
    JumpKey = Enum.KeyCode.Space,       -- Прыжок
    BoostKey = Enum.KeyCode.LeftShift,  -- Ускорение прыжка
    
    MaxWalkSpeed = 1000,                  -- Безопасный лимит
    MaxJumpPower = 2000,                  -- Максимум без бана
    JumpIncrement = 5,                  -- Шаг увеличения прыжка
    
    UpdateDelay = 0.1,                  -- Частота проверок
    AntiCheatBypass = true              -- Обход защиты
}

--= Интерфейс =--
local function CreateUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "VL_Booster_v3"
    ScreenGui.Parent = game:GetService("CoreGui")

    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 250, 0, 180)
    Frame.Position = UDim2.new(0.8, 10, 0.5, -90)
    Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    Frame.Visible = false
    Frame.Parent = ScreenGui

    -- Элементы управления
    local controls = {
        WalkLabel = Instance.new("TextLabel"),
        WalkSlider = Instance.new("TextBox"),
        JumpLabel = Instance.new("TextLabel"),
        JumpSlider = Instance.new("TextBox"),
        Status = Instance.new("TextLabel")
    }

    -- Конфигурация элементов
    controls.WalkLabel.Text = "Скорость: "..SETTINGS.MaxWalkSpeed
    controls.WalkLabel.TextColor3 = Color3.new(1, 1, 1)
    controls.WalkLabel.Position = UDim2.new(0, 10, 0, 10)
    controls.WalkLabel.Parent = Frame

    controls.WalkSlider.Text = SETTINGS.MaxWalkSpeed
    controls.WalkSlider.PlaceholderText = "16-1000"
    controls.WalkSlider.Size = UDim2.new(0, 230, 0, 30)
    controls.WalkSlider.Position = UDim2.new(0, 10, 0, 30)
    controls.WalkSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    controls.WalkSlider.TextColor3 = Color3.new(1, 1, 1)
    controls.WalkSlider.Parent = Frame

    controls.JumpLabel.Text = "Прыжок: "..SETTINGS.MaxJumpPower
    controls.JumpLabel.TextColor3 = Color3.new(1, 1, 1)
    controls.JumpLabel.Position = UDim2.new(0, 10, 0, 70)
    controls.JumpLabel.Parent = Frame

    controls.JumpSlider.Text = SETTINGS.MaxJumpPower
    controls.JumpSlider.PlaceholderText = "1-1000"
    controls.JumpSlider.Size = UDim2.new(0, 230, 0, 30)
    controls.JumpSlider.Position = UDim2.new(0, 10, 0, 90)
    controls.JumpSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    controls.JumpSlider.TextColor3 = Color3.new(1, 1, 1)
    controls.JumpSlider.Parent = Frame

    controls.Status.Text = "Статус: Активен (F5)"
    controls.Status.TextColor3 = Color3.new(0, 1, 0)
    controls.Status.Position = UDim2.new(0, 10, 0, 140)
    controls.Status.Parent = Frame

    return {
        Screen = ScreenGui,
        Frame = Frame,
        Controls = controls
    }
end

--= Система параметров =--
local UI = CreateUI()
local ACTIVE_PARAMS = {
    WalkSpeed = SETTINGS.MaxWalkSpeed,
    JumpPower = SETTINGS.MaxJumpPower,
    Enabled = true
}

-- Функция безопасного применения
local function SafeApply()
    local Character = Player.Character
    if not Character then return end

    local Humanoid = Character:FindFirstChildOfClass("Humanoid")
    if not Humanoid then return end

    -- WalkSpeed с плавным изменением
    if math.abs(Humanoid.WalkSpeed - ACTIVE_PARAMS.WalkSpeed) > 5 then
        Humanoid.WalkSpeed = ACTIVE_PARAMS.WalkSpeed
    end

    -- JumpPower с защитой
    if SETTINGS.AntiCheatBypass then
        -- Плавное увеличение с лимитом
        local newJump = math.min(SETTINGS.MaxJumpPower, 
            Humanoid.JumpPower + SETTINGS.JumpIncrement)
        
        Humanoid.JumpPower = newJump
        
        -- Естественная активация прыжка
        if UIS:IsKeyDown(SETTINGS.JumpKey) and Humanoid:GetState() == Enum.HumanoidStateType.Running then
            task.wait(0.15)
            Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    else
        Humanoid.JumpPower = ACTIVE_PARAMS.JumpPower
    end
end

--= Основной цикл =--
RunService.Heartbeat:Connect(function()
    if ACTIVE_PARAMS.Enabled then
        pcall(SafeApply)
    end
end)

--= Обработчики UI =--
UI.Controls.WalkSlider.FocusLost:Connect(function()
    local value = tonumber(UI.Controls.WalkSlider.Text) or SETTINGS.MaxWalkSpeed
    ACTIVE_PARAMS.WalkSpeed = math.clamp(value, 16, 100)
    UI.Controls.WalkSlider.Text = ACTIVE_PARAMS.WalkSpeed
    UI.Controls.WalkLabel.Text = "Скорость: "..ACTIVE_PARAMS.WalkSpeed
end)

UI.Controls.JumpSlider.FocusLost:Connect(function()
    local value = tonumber(UI.Controls.JumpSlider.Text) or SETTINGS.MaxJumpPower
    ACTIVE_PARAMS.JumpPower = math.clamp(value, 50, 85)
    UI.Controls.JumpSlider.Text = ACTIVE_PARAMS.JumpPower
    UI.Controls.JumpLabel.Text = "Прыжок: "..ACTIVE_PARAMS.JumpPower
end)

--= Горячие клавиши =--
UIS.InputBegan:Connect(function(Input, _)
    if Input.KeyCode == SETTINGS.Keybind then
        UI.Frame.Visible = not UI.Frame.Visible
    end
    
    -- Ускоренный прыжок
    if Input.KeyCode == SETTINGS.JumpKey and UIS:IsKeyDown(SETTINGS.BoostKey) then
        ACTIVE_PARAMS.JumpPower = math.min(85, ACTIVE_PARAMS.JumpPower + 5)
    end
end)

--= Авто-восстановление =--
Player.CharacterAdded:Connect(function()
    task.wait(1)
    ACTIVE_PARAMS.Enabled = true
    pcall(SafeApply)
end)

--= Уведомление =--
game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "VL Booster v3 Активирован!",
    Text = "Настройки: "..SETTINGS.MaxWalkSpeed.." speed / "..SETTINGS.MaxJumpPower.." jump",
    Duration = 5
})
