pcall(function()
	loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Performance-Overhaul-I-Universal-Anti-Lagger-128625"))()
end)

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

local function runQueue()
	if syn and syn.queue_on_teleport then
		syn.queue_on_teleport([[loadstring(game:HttpGet("https://raw.githubusercontent.com/sla35888/AntiLagRoblox/refs/heads/main/README.md"))()]])
	elseif queue_on_teleport then
		queue_on_teleport([[loadstring(game:HttpGet("https://raw.githubusercontent.com/sla35888/AntiLagRoblox/refs/heads/main/README.md"))()]])
	end
end
pcall(runQueue)

local localPlayer = Players.LocalPlayer or Players:GetPropertyChangedSignal("LocalPlayer"):Wait() and Players.LocalPlayer

local function destroyEffect(obj)
	if not obj or not obj.Parent then return end
	if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") or obj:IsA("Explosion") or obj:IsA("Highlight") or obj:IsA("PostEffect") or obj:IsA("PointLight") or obj:IsA("SurfaceLight") or obj:IsA("SpotLight") then
		pcall(function() obj:Destroy() end)
	elseif obj:IsA("Decal") or obj:IsA("Texture") then
		pcall(function() obj:Destroy() end)
	elseif obj:IsA("BasePart") then
		pcall(function()
			obj.Material = Enum.Material.Plastic
			obj.Reflectance = 0
			obj.CastShadow = false
		end)
	end
end

for _, descendant in ipairs(Workspace:GetDescendants()) do
	destroyEffect(descendant)
end
Workspace.DescendantAdded:Connect(destroyEffect)

local function cleanAvatar(character)
	for _, obj in ipairs(character:GetDescendants()) do
		if obj:IsA("Accessory") or obj:IsA("Shirt") or obj:IsA("Pants") or obj:IsA("ShirtGraphic") or obj:IsA("CharacterMesh") or obj:IsA("Clothing") or obj:IsA("BodyColors") then
			pcall(function() obj:Destroy() end)
		elseif obj:IsA("BasePart") then
			pcall(function()
				obj.Color = Color3.fromRGB(128, 128, 128)
				obj.Material = Enum.Material.Plastic
				obj.Reflectance = 0
				obj.CastShadow = false
			end)
		end
	end
end

local function optimizeCharacter(character)
	cleanAvatar(character)
	
	if character ~= localPlayer.Character then
		local humanoid = character:FindFirstChildOfClass("Humanoid")
		if humanoid then
			local animator = humanoid:FindFirstChildOfClass("Animator")
			if animator then
				pcall(function()
					for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
						track:Stop(0)
					end
				end)
			end
		end
		
		local animate = character:FindFirstChild("Animate")
		if animate and animate:IsA("LocalScript") then
			pcall(function() animate.Disabled = true end)
		end
		
		for _, desc in ipairs(character:GetDescendants()) do
			if desc:IsA("AnimationController") or desc:IsA("Animator") then
				pcall(function() desc:Destroy() end)
			end
		end
	end
	
	character.DescendantAdded:Connect(function(desc)
		destroyEffect(desc)
		if desc:IsA("Accessory") or desc:IsA("Shirt") or desc:IsA("Pants") or desc:IsA("ShirtGraphic") or desc:IsA("CharacterMesh") or desc:IsA("Clothing") or desc:IsA("BodyColors") then
			task.defer(function() pcall(function() desc:Destroy() end) end)
		elseif desc:IsA("BasePart") then
			pcall(function() desc.Color = Color3.fromRGB(128, 128, 128) end)
		elseif (desc:IsA("Animation") or desc:IsA("Animator") or desc:IsA("AnimationController")) and character ~= localPlayer.Character then
			task.defer(function()
				pcall(function()
					local hum = character:FindFirstChildOfClass("Humanoid")
					if hum then
						local anim = hum:FindFirstChildOfClass("Animator")
						if anim then
							for _, track in ipairs(anim.GetPlayingAnimationTracks and anim:GetPlayingAnimationTracks() or {}) do
								track:Stop(0)
							end
						end
					end
					if desc:IsA("Animator") or desc:IsA("AnimationController") then
						desc:Destroy()
					end
				end)
			end)
		end
	end)
end

if localPlayer.Character then
	optimizeCharacter(localPlayer.Character)
end
localPlayer.CharacterAdded:Connect(optimizeCharacter)

for _, player in ipairs(Players:GetPlayers()) do
	if player ~= localPlayer then
		if player.Character then optimizeCharacter(player.Character) end
		player.CharacterAdded:Connect(optimizeCharacter)
	end
end
Players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(optimizeCharacter)
end)

local function clearLighting(parent)
	for _, child in ipairs(parent:GetChildren()) do
		if child:IsA("Sky") or child:IsA("Atmosphere") or child:IsA("Clouds") or child:IsA("PostEffect") or child:IsA("PointLight") or child:IsA("SurfaceLight") or child:IsA("SpotLight") then
			pcall(function() child:Destroy() end)
		end
	end
end
clearLighting(Lighting)
Lighting.ChildAdded:Connect(function(child)
	if child:IsA("Sky") or child:IsA("Atmosphere") or child:IsA("Clouds") or child:IsA("PostEffect") or child:IsA("PointLight") or child:IsA("SurfaceLight") or child:IsA("SpotLight") then
		pcall(function() child:Destroy() end)
	end
end)

pcall(function()
	Lighting.GlobalShadows = false
	Lighting.Technology = Enum.Technology.Compatibility
end)

pcall(function()
	settings():GetService("RenderSettings").QualityLevel = Enum.QualityLevel.Level01
	settings():GetService("RenderSettings").MeshPartDetailLevel = Enum.MeshPartDetailLevel.Level01
end)

local function applyCamera()
	local camera = Workspace.CurrentCamera
	if camera then
		camera.FieldOfView = 120
	end
end

applyCamera()
Workspace:GetPropertyChangedSignal("CurrentCamera"):Connect(function()
	local camera = Workspace.CurrentCamera
	if camera then
		camera.FieldOfView = 120
		camera:GetPropertyChangedSignal("FieldOfView"):Connect(function()
			camera.FieldOfView = 120
		end)
	end
end)

if Workspace.CurrentCamera then
	Workspace.CurrentCamera:GetPropertyChangedSignal("FieldOfView"):Connect(function()
		Workspace.CurrentCamera.FieldOfView = 120
	end)
end
