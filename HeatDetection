local sservice = game:GetService("SocialService")
local plrs = game:GetService("Players")
local vcservice = game:GetService("VoiceChatService")

local LHTTP = require(script.Parent.HTTPSHandler)

local module = {}

local weightsTable = {
	youngAccount = 0.1,
	hiddenInventoryPenalty = 0.075,
	insufficientFriends = 0.15,
	followedFriendIntoGame = -0.15
} --Configures how much each paramater matters in calculation

local settings = {
	voiceChatBypass = true,
	suspiciousAge = 7, --In Days
	minFriends = 3,
	enableBadgeCheck = true, --Does nothing for now
	enableGamepassCheck = true, --Does nothing for now
	penalizeForHiddenInventory = true,
}

local proxyEndpoints = {
	--[[
	WARNING! THESE ENDPOINTS ARE NOT GOING TO WORK! ROBLOX API ENDPOINTS CANNOT BE CALLED FROM A ROBLOX GAME.
	THESE WILL FAIL AND YOU WILL NEED A PROXY!
	]]---
	hiddenInventoryCheck = "https://inventory.roblox.com/v1/users/%s/can-view-inventory"
}

local playerHeats = {
	
}

module.operationEnum = {
	ADD = "+",
	SUBTRACT = "-",
	DEFINE = "="
}

module.ModPlayerHeat = function(player: Player, mode --[[see module.operationEnum]], val)
	if mode == "+" then
		playerHeats[player.UserId] += val
	end
	if mode == "-" then
		playerHeats[player.UserId] -= val
	end
	if mode == "=" then
		playerHeats[player.UserId] = val
	end
end --Used for custom integrations, such as remote firing rate

plrs.PlayerAdded:Connect(function(player)
	module.checkHeat(player)
end)

plrs.PlayerRemoving:Connect(function(player)
	playerHeats[player.UserId] = nil
end)

module.checkHeat = function(player: Player)
	local heatProduct = 0.0
		
	if settings.voiceChatBypass and vcservice:IsVoiceEnabledForUserIdAsync() then
		print("User has bypassed heat detection as they have voice chat enabled ".. player.Name.. ":".. player.UserId)
	else
		print("Checking heat values for user ".. player.Name.. ":".. player.UserId)
		
		if player.AccountAge < settings.suspiciousAge then
			heatProduct += weightsTable.youngAccount
		else
			heatProduct -= weightsTable.youngAccount
		end

		if #plrs:GetFriendsAsync(player.UserId) > settings.minFriends then
			heatProduct += weightsTable.insufficientFriends
		end
		
		if settings.penalizeForHiddenInventory then
			local rqst = LHTTP.request((proxyEndpoints.hiddenInventoryCheck):format(player.UserId))
			if not rqst.canView then
				heatProduct += weightsTable.hiddenInventoryPenalty
			end
		end
		
		if player.FollowUserId ~= nil then
			heatProduct -= weightsTable.followedFriendIntoGame
		end
	end
		
	playerHeats[player.UserId] = heatProduct
	
	return heatProduct --Use this data to implement webhooks in Discord or something idk
end

return module
