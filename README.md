if not ExecutorSupport then
    local Players = game:GetService("Players")
    local executorSupport = {}

    function test(name: string, func: () -> (), ...)
        local success, _ = pcall(func, ...)
        executorSupport[name] = success
        return success
    end

    test("firesignal", function()
        local event = Instance.new("BindableEvent")
        event.Event:Once(function() end)
        firesignal(event.Event, true)
        event:Destroy()
    end)

    for name, result in pairs(executorSupport) do
        print(name .. ":", result)
    end

    getgenv().ExecutorSupport = executorSupport
end

-- Função para criar a GUI
local function createTestGUI()
    local player = Players.LocalPlayer
    local playerGui = player:WaitForChild("PlayerGui")
    local screenGui = Instance.new("ScreenGui")
    screenGui.Parent = playerGui  -- Adiciona a ScreenGui ao PlayerGui

    local frame = Instance.new("Frame", screenGui)
    frame.Size = UDim2.new(0.5, 0, 0.5, 0)
    frame.Position = UDim2.new(0.25, 0, 0.25, 0)
    frame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

    local textLabel = Instance.new("TextLabel", frame)
    textLabel.Size = UDim2.new(1, 0, 0.8, 0)
    textLabel.Text = "MSPAINT DOORS GUI"
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.BackgroundTransparency = 1

    local closeButton = Instance.new("TextButton", frame)
    closeButton.Size = UDim2.new(0.5, 0, 0.2, 0)
    closeButton.Position = UDim2.new(0.25, 0, 0.8, 0)
    closeButton.Text = "Fechar GUI"
    closeButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)

    closeButton.MouseButton1Click:Connect(function()
        screenGui:Destroy()
        print("GUI removida.")
    end)

    -- Função para permitir arrastar a GUI
    local dragging, dragStart, startPos = false, nil, nil

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging, dragStart, startPos = true, input.Position, frame.Position
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    frame.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
end

-- Carregar o script específico do jogo
print("Executor Trigon Mobile detectado, carregando o GUI...")

local success, err = pcall(function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/notpoiu/mspaint/main/places/" .. game.GameId .. ".lua"))()
end)

if not success then
    print("Falha ao carregar o GUI:", err)
else
    print("GUI carregada com sucesso!")
    createTestGUI()  -- Chame a função para criar a GUI de teste
end
