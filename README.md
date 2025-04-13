local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local AIMBOT_ENABLED = false
local TOGGLE_KEY = Enum.KeyCode.L -- Tecla para ativar/desativar

-- Toggle com tecla L
UserInputService.InputBegan:Connect(function(input, gp)
	if not gp and input.KeyCode == TOGGLE_KEY then
		AIMBOT_ENABLED = not AIMBOT_ENABLED
		warn("Aimbot: " .. (AIMBOT_ENABLED and "ATIVADO ✅" or "DESATIVADO ❌"))
	end
end)

-- Função para encontrar o jogador mais próximo da mira
local function getClosestPlayerToMouse()
	local closestPlayer = nil
	local shortestDistance = math.huge
	local mouseLocation = UserInputService:GetMouseLocation()

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			local head = player.Character.Head
			local pos, onScreen = Camera:WorldToViewportPoint(head.Position)
			if onScreen then
				local dist = (Vector2.new(pos.X, pos.Y) - mouseLocation).Magnitude
				if dist < shortestDistance and dist < 250 then -- Raio de mira
					shortestDistance = dist
					closestPlayer = player
				end
			end
		end
	end

	return closestPlayer
end

-- Mira na cabeça do alvo se estiver ativado
RunService.RenderStepped:Connect(function()
	if AIMBOT_ENABLED then
		local target = getClosestPlayerToMouse()
		if target and target.Character and target.Character:FindFirstChild("Head") then
			local head = target.Character.Head
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, head.Position)
		end
	end
end)
