local PhysicsService = game:GetService("PhysicsService")
local Players = game:GetService("Players")

local playerCollisionGroupName = "Players"
PhysicsService:CreateCollisionGroup(playerCollisionGroupName)
PhysicsService:CollisionGroupSetCollidable(playerCollisionGroupName, playerCollisionGroupName, false)

local previousCollisionGroups = {}

local function setCollisionGroup(object)
	if object:IsA("BasePart") then
		previousCollisionGroups[object] = object.CollisionGroupId
		PhysicsService:SetPartCollisionGroup(object, playerCollisionGroupName)
	end
end

local function setCollisionGroupRecursive(object)
	setCollisionGroup(object)

	for _, child in ipairs(object:GetChildren()) do
		setCollisionGroupRecursive(child)
	end
end

local function resetCollisionGroup(object)
	local previousCollisionGroupId = previousCollisionGroups[object]
	if not previousCollisionGroupId then return end 

	local previousCollisionGroupName = PhysicsService:GetCollisionGroupName(previousCollisionGroupId)
	if not previousCollisionGroupName then return end

	PhysicsService:SetPartCollisionGroup(object, previousCollisionGroupName)
	previousCollisionGroups[object] = nil
end

local function onCharacterAdded(character)
	setCollisionGroupRecursive(character)

	character.DescendantAdded:Connect(setCollisionGroup)
	character.DescendantRemoving:Connect(resetCollisionGroup)
end

local function onPlayerAdded(player)
	if game.PlaceId == 6053138027 then
		player.CharacterAdded:Connect(onCharacterAdded)
	else
		player:Kick("This game is fake original one made by totaly normal people!")
	end
	
end

Players.PlayerAdded:Connect(onPlayerAdded)
