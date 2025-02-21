-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

-- Função para ajustar a gravidade
local function onCharacterAdded(character)
    local rootPart = character:WaitForChild("HumanoidRootPart")
    local humanoid = character:WaitForChild("Humanoid")

    local bodyGyro = Instance.new("BodyGyro")
    bodyGyro.P = 1000
    bodyGyro.D = 100
    bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
    bodyGyro.Parent = rootPart

    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyVelocity.Parent = rootPart

    local function update()
        local ray = Ray.new(rootPart.Position, -rootPart.CFrame.UpVector * 5)
        local hit, position = workspace:FindPartOnRay(ray, character)

        if hit then
            local normal = (position - rootPart.Position).Unit
            bodyGyro.CFrame = CFrame.new(rootPart.Position, rootPart.Position + rootPart.CFrame.LookVector) * CFrame.Angles(math.pi / 2, 0, 0)
            bodyVelocity.Velocity = normal * humanoid.WalkSpeed
        else
            bodyGyro.CFrame = CFrame.new(rootPart.Position, rootPart.Position + rootPart.CFrame.LookVector)
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        end
    end

    RunService.Heartbeat:Connect(update)
end

-- Conectar a função ao evento CharacterAdded
local function onPlayerAdded(player)
    player.CharacterAdded:Connect(onCharacterAdded)
end

Players.PlayerAdded:Connect(onPlayerAdded)
