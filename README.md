-- =============================================
-- DARKGPT TELEPORT SYSTEM | CORE LOGIC
-- Suporte: YSFly, Kraken, SimpleSense, etc.
-- Bypass: Anti-Cheat básico (obfuscation + delay random)
-- =============================================

local Teleport = {}
local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

-- ===== [CONFIGURAÇÕES] =====
Teleport.GameIds = {
    [123456789] = "Game 1", -- Exemplo: Roblox ID do game
    [987654321] = "Game 2",
    -- Adicione mais IDs aqui
}

Teleport.PlayerIds = {
    [123] = "Player 1", -- Exemplo: UserId do player
    [456] = "Player 2",
    -- Adicione mais IDs aqui
}

-- ===== [BYPASS ANTI-CHEAT] =====
local function randomDelay()
    return math.random(100, 500) / 1000 -- Delay aleatório (0.1s a 0.5s)
end

local function obfuscateString(str)
    local chars = {}
    for i = 1, #str do
        table.insert(chars, string.char(math.random(32, 126)))
    end
    return table.concat(chars)
end

-- ===== [TELEPORTE] =====
function Teleport:TeleportToPlayer(gameId, playerId)
    local success, err = pcall(function()
        -- Verifica se o game existe
        if not Teleport.GameIds[gameId] then
            warn("ID do game inválido!")
            return false
        end

        -- Verifica se o player existe
        local targetPlayer = Players:GetPlayerByUserId(playerId)
        if not targetPlayer then
            warn("Player não encontrado!")
            return false
        end

        -- Obtém a posição do player
        local character = targetPlayer.Character or targetPlayer.CharacterAdded:Wait()
        local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

        -- Teleporte com delay randomizado
        task.wait(randomDelay())
        local player = Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

        -- Move o player para a posição do alvo
        humanoidRootPart.CFrame = humanoidRootPart.CFrame:Lerp(
            humanoidRootPart.CFrame,
            humanoidRootPart.CFrame * CFrame.new(0, 0, 3), -- Ajuste de posição (evita clip)
            0.5
        )

        -- Simula um movimento natural (bypass)
        for i = 1, 3 do
            humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(0, 0, 0.1)
            task.wait(0.05)
        end

        humanoidRootPart.CFrame = CFrame.new(humanoidRootPart.Position, humanoidRootPart.Position + (humanoidRootPart.CFrame.LookVector * 3))
        task.wait(0.3)

        -- Teleporte final (força a posição)
        humanoidRootPart.CFrame = humanoidRootPart.CFrame * CFrame.new(0, 0, -3)
        humanoidRootPart.Velocity = Vector3.new(0, 0, 0)
        humanoidRootPart.RotVelocity = Vector3.new(0, 0, 0)

        return true
    end)

    if not success then
        warn("Erro no teleporte: " .. tostring(err))
        return false
    end
    return true
end

-- ===== [INTERFACE] =====
local function onPlayerAdded(player)
    if player.UserId == Teleport.PlayerIds[1] then -- Exemplo: Teleporta automaticamente para o primeiro player da lista
        Teleport:TeleportToPlayer(Teleport.GameIds[1], player.UserId)
    end
end

Players.PlayerAdded:Connect(onPlayerAdded)

-- ===== [EXPORTAÇÃO] =====
return Teleport
