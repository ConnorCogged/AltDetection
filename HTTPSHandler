local module = {}

local HttpService = game:GetService("HttpService")

--ez copy paste https://create.roblox.com/docs/reference/engine/classes/HttpService ecksdee
module.request = function(url)
	local response
	local data
	-- Use pcall in case something goes wrong
	pcall(function()
		response = HttpService:GetAsync(url)
		data = HttpService:JSONDecode(response)
	end)
	
	if not data then
		return nil
	end

	return data
end

return module
