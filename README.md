-- Screen GUI principal
local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = player:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- Frame principal (menu transparente)
local MenuFrame = Instance.new("Frame")
MenuFrame.Name = "MenuFrame"
MenuFrame.Parent = ScreenGui
MenuFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MenuFrame.BackgroundTransparency = 0.6
MenuFrame.Size = UDim2.new(0, 220, 0, 300)
MenuFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MenuFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
MenuFrame.Visible = true

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0.1, 0)
UICorner.Parent = MenuFrame

-- Botão para abrir/fechar menu
local ToggleButton = Instance.new("TextButton")
ToggleButton.Parent = ScreenGui
ToggleButton.Size = UDim2.new(0, 70, 0, 25)
ToggleButton.Position = UDim2.new(0.02, 0, 0.05, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
ToggleButton.TextColor3 = Color3.new(0, 0, 0)
ToggleButton.Text = "Menu"
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 16
local cornerBtn = Instance.new("UICorner")
cornerBtn.CornerRadius = UDim.new(0.1, 0)
cornerBtn.Parent = ToggleButton

ToggleButton.MouseButton1Click:Connect(function()
    MenuFrame.Visible = not MenuFrame.Visible
end)

-- Função para criar botões
local function criarBotao(texto, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Parent = MenuFrame
    btn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    btn.BackgroundTransparency = 0.4
    btn.Position = UDim2.new(0.05, 0, yPos, 0)
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Font = Enum.Font.SourceSansBold
    btn.Text = texto
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextSize = 18
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0.1, 0)
    corner.Parent = btn
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- ===== ESP MÁQUINA =====
local espMachinesEnabled, espMachinesConn = false, nil
local function toggleESPMaquina()
    espMachinesEnabled = not espMachinesEnabled
    if espMachinesEnabled then
        espMachinesConn = RunService.RenderStepped:Connect(function()
            for _, part in pairs(workspace:GetDescendants()) do
                if part.Name:lower():find("generator") or part.Name:lower():find("machine") then
                    if not part:FindFirstChild("ESPHighlight") then
                        local hl = Instance.new("Highlight", part)
                        hl.Name = "ESPHighlight"
                        hl.OutlineColor = Color3.fromRGB(0, 0, 255)
                        hl.FillTransparency = 0.7
                    end
                end
            end
        end)
    else
        if espMachinesConn then espMachinesConn:Disconnect() end
        for _, part in pairs(workspace:GetDescendants()) do
            local hl = part:FindFirstChild("ESPHighlight")
            if hl then hl:Destroy() end
        end
    end
end
criarBotao("ESP Máquina", 0.05, toggleESPMaquina)

-- ===== ESP PLAYER >200HP =====
local espPlayersEnabled, espPlayersConn = false, nil
local function toggleESPPlayers()
    espPlayersEnabled = not espPlayersEnabled
    if espPlayersEnabled then
        espPlayersConn = RunService.RenderStepped:Connect(function()
            for _, plr in pairs(game.Players:GetPlayers()) do
                if plr ~= player and plr.Character and plr.Character:FindFirstChild("Humanoid") then
                    local hum = plr.Character.Humanoid
                    if hum.Health > 200 then
                        if not plr.Character:FindFirstChild("ESPPlayerHighlight") then
                            local hl = Instance.new("Highlight", plr.Character)
                            hl.Name = "ESPPlayerHighlight"
                            hl.OutlineColor = Color3.new(1, 0, 0)
                            hl.FillTransparency = 0.7
                        end
                    else
                        local hl = plr.Character:FindFirstChild("ESPPlayerHighlight")
                        if hl then hl:Destroy() end
                    end
                end
            end
        end)
    else
        if espPlayersConn then espPlayersConn:Disconnect() end
        for _, plr in pairs(game.Players:GetPlayers()) do
            local hl = plr.Character and plr.Character:FindFirstChild("ESPPlayerHighlight")
            if hl then hl:Destroy() end
        end
    end
end
criarBotao("ESP Player >200HP", 0.25, toggleESPPlayers)

-- ===== TP =====
local tpEnabled = false
local function toggleTP()
    tpEnabled = not tpEnabled
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        if tpEnabled then
            char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame - Vector3.new(0, 50, 0)
        else
            char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame + Vector3.new(0, 50, 0)
        end
    end
end
criarBotao("TP", 0.45, toggleTP)

-- ===== ESP KIT MÉDICO =====
local espMedkitEnabled, espMedkitConn = false, nil
local function toggleESPMedkit()
    espMedkitEnabled = not espMedkitEnabled
    if espMedkitEnabled then
        espMedkitConn = RunService.RenderStepped:Connect(function()
            for _, part in pairs(workspace:GetDescendants()) do
                if part:IsA("Model") or part:IsA("Part") then
                    local nameLower = part.Name:lower()
                    if nameLower:find("medkit") or nameLower:find("firstaid") then
                        if not part:FindFirstChild("ESPMedkitHighlight") then
                            local hl = Instance.new("Highlight", part)
                            hl.Name = "ESPMedkitHighlight"
                            hl.OutlineColor = Color3.fromRGB(0, 255, 0)
                            hl.FillTransparency = 0.7
                        end
                    end
                end
            end
        end)
    else
        if espMedkitConn then espMedkitConn:Disconnect() end
        for _, part in pairs(workspace:GetDescendants()) do
            local hl = part:FindFirstChild("ESPMedkitHighlight")
            if hl then hl:Destroy() end
        end
    end
end
criarBotao("ESP Kit Médico", 0.65, toggleESPMedkit)
