
ClosestPlrFromMouse = function()
    local Target, Closest = nil, 1/0
 
    for _ ,v in pairs(Players:GetPlayers()) do
        if (v.Character and v ~= Client and v.Character:FindFirstChild("HumanoidRootPart")) then
            local Position, OnScreen = Camera:WorldToScreenPoint(v.Character.HumanoidRootPart.Position)
            local Distance = (Vector2.new(Position.X, Position.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude

            if (Circle.Radius > Distance and Distance < Closest and OnScreen) then
                Closest = Distance
                Target = v
            end
        end
    end
    return Target
end

local WTS = function (Object)
    local ObjectVector = Camera:WorldToScreenPoint(Object.Position)
    return Vector2.new(ObjectVector.X, ObjectVector.Y)
end

local IsOnScreen = function (Object)
    local IsOnScreen = Camera:WorldToScreenPoint(Object.Position)
    return IsOnScreen
end

local FilterObjs = function (Object)
    if string.find(Object.Name, "Gun") then
        return
    end
    if table.find({"Part", "MeshPart", "BasePart"}, Object.ClassName) then
        return true
    end
end

local GetClosestBodyPart = function (character)
    local ClosestDistance = 1/0
    local BodyPart = nil
    if (character and character:GetChildren()) then
        for _,  x in next, character:GetChildren() do
            if FilterObjs(x) and IsOnScreen(x) then
                local Distance = ((WTS(x) - Vector2.new(Mouse.X, Mouse.Y))/SoulTaker.SoulTakerSilent.DistanceDivided).Magnitude
                if (Circle.Radius > Distance and Distance < ClosestDistance) then
                    ClosestDistance = Distance
                    BodyPart = x
                end
            end
        end
    end
    return BodyPart
end


local Prey
local PartToUse = SoulTaker.SoulTakerSilent.Part

task.spawn(function ()
    while task.wait() do
        if Prey then
            if SoulTaker.SoulTakerSilent.Enabled and SoulTaker.SoulTakerSilent.ClosestPart == true then
                PartToUse = tostring(GetClosestBodyPart(Prey.Character))
            end
            if SoulTaker.Config.UnlockOnDeath == true then
                if Prey.Character.Humanoid.Health < 2 then
                    Prey = nil
                end
            end
            if SoulTaker.Config.UnlockOnYourDeath == true then
                if Client.Character.Humanoid.Health < 2 then
                    Prey = nil
                end
            end
			if SoulTaker.Resolver.Enabled[1] == true then
				local playertoresolve = Prey.Character.HumanoidRootPart
				if playertoresolve.Velocity.X > SoulTaker.Resolver.Enabled[2] or playertoresolve.Velocity.X < SoulTaker.Resolver.Enabled[3] or playertoresolve.Velocity.Y > SoulTaker.Resolver.Enabled[2] or playertoresolve.Velocity.Y < SoulTaker.Resolver.Enabled[3] or playertoresolve.Velocity.Z > SoulTaker.Resolver.Enabled[2] or playertoresolve.Velocity.Z < SoulTaker.Resolver.Enabled[3] then
					playertoresolve.Velocity = Vector3.new(0, 0, 0)
				end
			end
			if SoulTaker.Config.AntiGroundShots then
				pcall(function()
                    local TargetVelv5 = Prey.Character[SoulTaker.SoulTakerSilent.Part]
                    TargetVelv5.Velocity = Vector3.new(TargetVelv5.Velocity.X, (TargetVelv5.Velocity.Y * 0.5), TargetVelv5.Velocity.Z)
                    TargetVelv5.AssemblyLinearVelocity = Vector3.new(TargetVelv5.Velocity.X, (TargetVelv5.Velocity.Y * 0.5), TargetVelv5.Velocity.Z)
                end)
			end
			if SoulTaker.Resolver.DisableOnAnti.Enabled == true and Prey.Character and Prey.Character:FindFirstChild(Sub.AIM.AIM_PART) then
				local playertoresolve = Prey.Character.HumanoidRootPart
				if playertoresolve.Velocity.X > SoulTaker.Resolver.DisableOnAnti.Max or playertoresolve.Velocity.X < SoulTaker.Resolver.DisableOnAnti.Min or playertoresolve.Velocity.Y > SoulTaker.Resolver.DisableOnAnti.Max or playertoresolve.Velocity.Y < SoulTaker.Resolver.DisableOnAnti.Min or playertoresolve.Velocity.Z > SoulTaker.Resolver.DisableOnAnti.Max or playertoresolve.Velocity.Z < SoulTaker.Resolver.DisableOnAnti.Min then
					AimlockTarget = nil
				end
			end
        end
    end
end)

local grmt = getrawmetatable(game)
local backupindex = grmt.__index
setreadonly(grmt, false)

grmt.__index = newcclosure(function(self, v)
    if (SoulTaker.SoulTakerSilent.Enabled and Mouse and tostring(v) == "Hit") then

        Prey = ClosestPlrFromMouse()

        if Prey then
            local endpoint = game.Players[tostring(Prey)].Character[PartToUse].CFrame + (
                game.Players[tostring(Prey)].Character[PartToUse].Velocity * SoulTaker.SoulTakerSilent.Pred
            )
            return (tostring(v) == "Hit" and endpoint)
        end
    end
    return backupindex(self, v)
end)



local Script = {Functions = {}}
    Script.Functions.getToolName = function(name)
        local split = string.split(string.split(name, "[")[2], "]")[1]
        return split
    end
    Script.Functions.getEquippedWeaponName = function()
        if (Client.Character) and Client.Character:FindFirstChildWhichIsA("Tool") then
           local Tool =  Client.Character:FindFirstChildWhichIsA("Tool")
           if string.find(Tool.Name, "%[") and string.find(Tool.Name, "%]") and not string.find(Tool.Name, "Wallet") and not string.find(Tool.Name, "Phone") then
              return Script.Functions.getToolName(Tool.Name)
           end
        end
        return nil
    end
    RS.RenderStepped:Connect(function()
    if Script.Functions.getEquippedWeaponName() ~= nil then
        local WeaponSoulTaker = SoulTaker.FOV.GunFOV[Script.Functions.getEquippedWeaponName()]
        if WeaponSoulTaker ~= nil and SoulTaker.FOV.GunFOV.Enabled == true then
            SoulTaker.FOV.Radius = WeaponSoulTaker.FOV 
        else
            SoulTaker.FOV.Radius = SoulTaker.FOV.Radius
        end
    end
end)

----------------settings















getgenv().CurrentCamera = game:GetService "Workspace".CurrentCamera







------------VARIABLES--------------
local services = setmetatable({ }, {
	__index = function(t,k)
		return game:GetService(k)
	end
})
local Settings = getgenv().SoulTaker.Settings
local Uisf = services.UserInputService
local Workgalaxy = services.Workspace
local RService = services.RunService
local SGui = services.StarterGui
local Inset2 = game:GetService("GuiService"):GetGuiInset().Y
local mousef = game.Players.LocalPlayer:GetMouse()
local Clientuser = Players.LocalPlayer
local runshit = game:GetService("RunService")
local localnigger = game.Players.LocalPlayer
local CF = CFrame.new
local RNew = Ray.new
local Vec3 = Vector3.new
local Vec2 = Vector2.new
local Sub = getgenv().SoulTaker.TRACER
local Aimlock, MousePressed, CanLockOn = true, false, true
local AimlockTarget, OldPre


-----GET THE NEAREST PLAYER -----
local GetNearestTarget = function()
	local plr1g2 = nil;
	local distance = Sub.AIM.DISTANCE;

	for i, v in next, Players:GetPlayers() do
		if v.Name ~= Clientuser.Name then
			--if v.Character and v.Team ~= Clientuser.Team then
			if v.Character then
				local pos,aaaa = Camera:WorldToViewportPoint(v.Character.Head.Position);
				local mouse = Uisf:GetMouseLocation()
				local magnitude = (Vec2(pos.X, pos.Y) - Vec2(mousef.X, mousef.Y)).magnitude;

				if aaaa and (magnitude < distance) then
					plr1g2 = v;
					distance = magnitude;
				end
			end
		end
	end
	return plr1g2;
end

--- BUTTON TO LOCK ON FUNCTION ----
mousef.KeyDown:Connect(function(k)
	if k ~= Sub.AIM.KEYBIND then 
		return
	elseif Aimlock and AimlockTarget == nil then
		CanLockOn = true
		if MousePressed ~= true then
			MousePressed = true
		end 
		local Target;Target = GetNearestTarget()
		if Target ~= nil then
			AimlockTarget = Target
		end
	else
		if AimlockTarget ~= nil then AimlockTarget = nil end
		if MousePressed ~= false then 
			MousePressed = false 
		end
	end
end)

local smoothnessTOuse = Sub.SMOOTHNESS.SMOOTHNESS_AMOUNT
local easingStyle = Sub.CONFIG.EASING_STYLE



---AIMLOCK PART----
RService.RenderStepped:Connect(function()
	if Aimlock == true and MousePressed == true and Sub.AIM.ENABLED == true then 
		if AimlockTarget and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then 
			if CanLockOn == true then
				if Sub.CONFIG.PREDICT_MOVEMENT == true then
					if Sub.SMOOTHNESS.USE_SMOOTHNESS == true then
						local Main = CF(Camera.CFrame.p, AimlockTarget.Character[Sub.AIM.AIM_PART].Position + AimlockTarget.Character[Sub.AIM.AIM_PART].Velocity/Sub.CONFIG.PREDICTION)
						Camera.CFrame = Camera.CFrame:Lerp(Main, smoothnessTOuse, easingStyle, Enum.EasingDirection.InOut)
					end
				elseif Sub.CONFIG.PREDICT_MOVEMENT == false then 
					if Sub.SMOOTHNESS.USE_SMOOTHNESS == true then
						local Main = CF(Camera.CFrame.p, AimlockTarget.Character[Sub.AIM.AIM_PART].Position)
						Camera.CFrame = Camera.CFrame:Lerp(Main, smoothnessTOuse, easingStyle, Enum.EasingDirection.InOut)
					end
				end
			end
		end
		if Aimlock == true and MousePressed == true and Sub.AIM.ENABLED == true then
			if Sub.CONFIG.UNLOCK_ON_DEATH == true and AimlockTarget ~= nil and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then
				local BE = AimlockTarget.Character.Humanoid
				if BE.Health < 5 then
					AimlockTarget = nil   
				end
			end
			if Sub.CONFIG.UNLOCK_ON_YOURDEATH == true and AimlockTarget ~= nil and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then
				local YBE = game.Players.LocalPlayer.Character.Humanoid
				if YBE.Health < 5 then
					AimlockTarget = nil   
				end
			end
			if Sub.SMOOTHNESS.SMOOTHNESS_Y == true and AimlockTarget ~= nil and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then
                if AimlockTarget.Character.Humanoid.Jump == true then
                    smoothnessTOuse = Sub.SMOOTHNESS.SMOOTHNESS_Y_VALUE
                else
                    smoothnessTOuse = Sub.SMOOTHNESS.SMOOTHNESS_AMOUNT
                end
			end
			if Sub.CONFIG.USE_JUMP_EASING == true and AimlockTarget and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then
                if AimlockTarget.Character.Humanoid.Jump == true then
                    easingStyle = Sub.CONFIG.JUMP_EASING_STYLE
                else
                    easingStyle = Sub.CONFIG.EASING_STYLE
                end
			end
			if Sub.AIM.CHECK_IF_JUMPED == true and AimlockTarget and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then
                if AimlockTarget.Character.Humanoid.Jump == true then
                    Sub.AIM.AIM_PART = Sub.AIM.CHECK_IF_JUMPED_AIMPART
                else
                    Sub.AIM.AIM_PART = Sub.AIM.AIM_PART
                end
			end
			if Sub.Config.RELOAD_CHECK == true and AimlockTarget and AimlockTarget.Character then
				local plr = game.Players.LocalPlayer
				local BackPack = plr.Backpack
				for _, gunName in pairs(Sub.CONFIG.RELOAD_CHECK.Guns) do
					local gun = BackPack:FindFirstChild(gunName)
					if gun then
						gun.Equipped:Connect(function()
							mousef.KeyDown:Connect(function(Reload)
								if Reload.KeyCode == Enum.KeyCode.R then
									if gun.Ammo.Value < gun.MaxAmmo.Value then
										AimlockTarget = nil
									end
								end
							end)
						end)
					end
				end
			end
			if Sub.CONFIG.DISABLE_ON_ANTI.Enabled == true and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then
				local playertoresolve = AimlockTarget.Character.HumanoidRootPart
				if playertoresolve.Velocity.X > Sub.CONFIG.DISABLE_ON_ANTI.Max or playertoresolve.Velocity.X < Sub.CONFIG.DISABLE_ON_ANTI.Min or playertoresolve.Velocity.Y > Sub.CONFIG.DISABLE_ON_ANTI.Max[2] or playertoresolve.Velocity.Y < Sub.CONFIG.DISABLE_ON_ANTI.Min[3] or playertoresolve.Velocity.Z > Sub.CONFIG.DISABLE_ON_ANTI.Max or playertoresolve.Velocity.Z < Sub.CONFIG.DISABLE_ON_ANTI.Min then
					AimlockTarget = nil
				end
			end
            if Sub.SMOOTHNESS.SHAKE == true and AimlockTarget and AimlockTarget.Character and CanLockOn == true then
                local Main = CFrame.new(Camera.CFrame.p, AimlockTarget.Character[Sub.AIM.AIM_PART].Position + AimlockTarget.Character[Sub.AIM.AIM_PART].Velocity/Sub.CONFIG.PREDICTION +
				Vector3.new(
					math.random(-Sub.SMOOTHNESS.SHAKE_VALUE.X, Sub.SMOOTHNESS.SHAKE_VALUE.X),
					math.random(-Sub.SMOOTHNESS.SHAKE_VALUE.Y, Sub.SMOOTHNESS.SHAKE_VALUE.Y),
					math.random(-Sub.SMOOTHNESS.SHAKE_VALUE.Z, Sub.SMOOTHNESS.SHAKE_VALUE.Z)
				) * 0.1)
				Camera.CFrame = Camera.CFrame:Lerp(Main, smoothnessTOuse, easingStyle, Enum.EasingDirection.InOut)
			else
                if AimlockTarget and AimlockTarget.Character and AimlockTarget.Character:FindFirstChild(Sub.AIM.AIM_PART) then 
                    if CanLockOn == true then
                        if Sub.CONFIG.PREDICT_MOVEMENT == true then
                            if Sub.SMOOTHNESS.USE_SMOOTHNESS == true then
                                local Main = CF(Camera.CFrame.p, AimlockTarget.Character[Sub.AIM.AIM_PART].Position + AimlockTarget.Character[Sub.AIM.AIM_PART].Velocity/Sub.CONFIG.PREDICTION)
                                Camera.CFrame = Camera.CFrame:Lerp(Main, smoothnessTOuse, easingStyle, Enum.EasingDirection.InOut)
                            end
                        elseif Sub.CONFIG.PREDICT_MOVEMENT == false then 
                            if Sub.SMOOTHNESS.USE_SMOOTHNESS == true then
                                local Main = CF(Camera.CFrame.p, AimlockTarget.Character[Sub.AIM.AIM_PART].Position)
                                Camera.CFrame = Camera.CFrame:Lerp(Main, smoothnessTOuse, easingStyle, Enum.EasingDirection.InOut)
                            end
                        end
                    end
                end
            end
		end
	end
end)

	local Circle2       = Drawing.new("Circle")  
	Circle2.Color           = Color3.new(1,1,1)
	Circle2.Thickness       = 1

	function Sub.UpdateFOV()
		if (not Circle2) then
			return Circle2
		end

		Circle2.Visible  = Sub.FOV.SHOW_FOV
		Circle2.Radius   = Sub.FOV.FOV_SIDES * 3
		Circle2.Position = Vector2.new(mousef.X, mousef.Y + (game:GetService("GuiService"):GetGuiInset().Y))
		return Circle2
	end

	RService.Heartbeat:Connect(function()
		Sub.UpdateFOV()
	end)

	pcall(function()
		if Sub.FOV.FOV_COLOR == "Red" then
			Circle2.Color = Color3.new(255, 0, 0)
		elseif Sub.FOV.FOV_COLOR == "Black" then
			Sub.FOV.FOV_COLOR = Color3.new(0, 0, 0)
		elseif Sub.FOV.FOV_COLOR == "Purple" then
			Circle2.Color = Color3.new(230, 230, 250)
		elseif Sub.FOV.FOV_COLOR == "Pink" then
			Circle2.Color = Color3.new(159, 43, 104)
		elseif Sub.FOV.FOV_COLOR == "Yellow" then
			Circle2.Color = Color3.new(255, 255, 0)
		elseif Sub.FOV.FOV_COLOR == "Grey" then
			Circle2.Color = Color3.new(128, 128, 128)
		elseif Sub.FOV.FOV_COLOR == "Blue" then
			Circle2.Color = Color3.new(173, 216, 230)
		elseif Sub.FOV.FOV_COLOR == "White" then
			Circle2.Color = Color3.new(1,1,1)
		end
	end)






local function callback(Text)
 if Text == "Button1 text" then
  print ("Answer")
elseif Text == ("Button2 text") then
 print ("Answer2")
 end
end
 
local NotificationBindable = Instance.new("BindableFunction")
NotificationBindable.OnInvoke = callback
--
game.StarterGui:SetCore("SendNotification",  {
 Title = "Thanks for Buying SoulTaker";
 Text = ".gg/SoulTaker.CC";
 Icon = "";
 Duration = 5;
 Button1 = "I joined";
 Button2 = "I will join";
 Callback = NotificationBindable;
})
 
