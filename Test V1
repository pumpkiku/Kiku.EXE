local _bypassWalkspeedActive = false
local _bypassJumpPowerActive = false

local old_index = nil; 
old_index = hookmetamethod(game, '__index', function(obj, idx)
    if typeof(obj) == 'userdata' and obj:IsA('Humanoid') and idx == 'WalkSpeed' then
        if _bypassWalkspeedActive then
            return 16
        end
    end    
    return old_index(obj, idx)
end)

local old_newindex = nil; 
old_newindex = hookmetamethod(game, '__newindex', function(obj, idx, val)
    if typeof(obj) == 'userdata' and obj:IsA('Humanoid') and idx == 'JumpPower' then
        if _bypassJumpPowerActive then
            val = 50
        end
    end    
    return old_newindex(obj, idx, val)
end)

local xScript = {}
xScript.Services = {}
xScript.Version = "1.05"

setmetatable(xScript.Services, {
	__index = function(_, service)
		return game:GetService(service)
	end,
	__newindex = function(t, i, v)
		t[i] = nil 
		warn("xScript.Services: Attempted to add service '"..tostring(i).."'. Custom services cannot be added this way.")
		return
	end
})

print("[0/2] Loading Extensions")

for i,v in pairs(getgenv().xScript.Extensions or {}) do
    if v == "test" then
        local success, result = pcall(function()
            return loadstring("https://raw.githubusercontent.com/DenizAf4can/xScript/main/"..v)()
        end)
        if success then
            xScript.test = result
            print("Loaded "..v, xScript.test and xScript.test.Author or "No Author Specified")
        else
            warn("Failed to load extension "..v..": "..tostring(result))
        end
    end
end

print("[1/2] Loading Functions")

function xScript.GetClipboard()
    if typeof(getclipboard) == "function" then
        return getclipboard()
    else
        warn("xScript.GetClipboard: 'getclipboard' function is not available in this environment.")
        return ""
    end
end

function xScript.GetLocalPlayer()
    return game:GetService("Players").LocalPlayer
end

function xScript.GetPlayerPosition(playerName)
    local player = xScript.GetPlayer(playerName)
    if not player then warn("xScript.GetPlayerPosition: Player not found: "..tostring(playerName)); return nil end
    local char = player.Character
    if not char then warn("xScript.GetPlayerPosition: Character not found for player: "..tostring(playerName)); return nil end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then warn("xScript.GetPlayerPosition: HumanoidRootPart not found for player: "..tostring(playerName)); return nil end
    return hrp.CFrame.Position
end

function xScript.GetPosition(object)
    if object then
        if object:IsA("BasePart") then
            return object.CFrame.Position
        elseif object:IsA("Model") and object.PrimaryPart then
            return object.PrimaryPart.CFrame.Position
        elseif typeof(object) == "CFrame" then
            return object.Position
        elseif typeof(object) == "Vector3" then
            return object
        end
    end
    warn("xScript.GetPosition: Invalid object type or object is nil.")
    return nil
end

function xScript.GetLocalCharacter()
    local player = xScript.GetLocalPlayer()
    if not player then return nil end
    return player.Character
end

function xScript.GetPlayer(playerName)
    return game:GetService("Players"):FindFirstChild(playerName)
end

function xScript.GetCharacter(playerName)
    local player = xScript.GetPlayer(playerName)
    if not player then return nil end
    return player.Character
end

function xScript.GetHwid()
    return game:GetService("RbxAnalyticsService"):GetClientId()
end

function xScript.GetHealth(playerName)
    local player = xScript.GetPlayer(playerName)
    if not player then warn("xScript.GetHealth: Player not found: "..tostring(playerName)); return nil end
    local char = player.Character
    if not char then warn("xScript.GetHealth: Character not found for player: "..tostring(playerName)); return nil end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid then warn("xScript.GetHealth: Humanoid not found for player: "..tostring(playerName)); return nil end
    return humanoid.Health
end

function xScript.GetWalkspeed(playerName)
    local player = xScript.GetPlayer(playerName)
    if not player then warn("xScript.GetWalkspeed: Player not found: "..tostring(playerName)); return nil end
    local char = player.Character
    if not char then warn("xScript.GetWalkspeed: Character not found for player: "..tostring(playerName)); return nil end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid then warn("xScript.GetWalkspeed: Humanoid not found for player: "..tostring(playerName)); return nil end
    return humanoid.WalkSpeed
end

function xScript.GetJumpPower(playerName)
    local player = xScript.GetPlayer(playerName)
    if not player then warn("xScript.GetJumpPower: Player not found: "..tostring(playerName)); return nil end
    local char = player.Character
    if not char then warn("xScript.GetJumpPower: Character not found for player: "..tostring(playerName)); return nil end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid then warn("xScript.GetJumpPower: Humanoid not found for player: "..tostring(playerName)); return nil end
    return humanoid.JumpPower
end

function xScript.GetChildrenOfType(instance, className)
    local foundChildren = {}
    if not instance then warn("xScript.GetChildrenOfType: Instance is nil."); return foundChildren end
    if not typeof(className) == "string" then warn("xScript.GetChildrenOfType: Invalid className provided (must be string)."); return foundChildren end
    for _, child in ipairs(instance:GetChildren()) do
        if child:IsA(className) then
            table.insert(foundChildren, child)
        end
    end
    return foundChildren
end

function xScript.GetDescendantsOfType(instance, className)
    local foundDescendants = {}
    if not instance then warn("xScript.GetDescendantsOfType: Instance is nil."); return foundDescendants end
    if not typeof(className) == "string" then warn("xScript.GetDescendantsOfType: Invalid className provided (must be string)."); return foundDescendants end
    for _, descendant in ipairs(instance:GetDescendants()) do
        if descendant:IsA(className) then
            table.insert(foundDescendants, descendant)
        end
    end
    return foundDescendants
end

function xScript.GetNearestPlayer(maxDistance)
    local localPlayer = xScript.GetLocalPlayer()
    local localCharacter = localPlayer and localPlayer.Character
    if not localCharacter then warn("xScript.GetNearestPlayer: Local character not found."); return nil end
    local localHRP = localCharacter:FindFirstChild("HumanoidRootPart")
    if not localHRP then warn("xScript.GetNearestPlayer: Local HumanoidRootPart not found."); return nil end

    local nearestPlayer = nil
    local minDistance = maxDistance or math.huge

    for _, player in ipairs(xScript.Services.Players:GetPlayers()) do
        if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = player.Character.HumanoidRootPart
            local distance = (localHRP.Position - hrp.Position).magnitude
            if distance < minDistance then
                minDistance = distance
                nearestPlayer = player
            end
        end
    end
    return nearestPlayer
end

function xScript.GetPlayersInRadius(centerReference, radius)
    local playersInRadius = {}
    local centerPos = xScript.GetPosition(centerReference)

    if not centerPos then warn("xScript.GetPlayersInRadius: Invalid center reference provided (not Vector3, CFrame, or BasePart/Model)."); return playersInRadius end
    if not typeof(radius) == "number" or radius < 0 then warn("xScript.GetPlayersInRadius: Invalid radius provided (must be a non-negative number)."); return playersInRadius end

    for _, player in ipairs(xScript.Services.Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = player.Character.HumanoidRootPart
            local distance = (centerPos - hrp.Position).magnitude
            if distance <= radius then
                table.insert(playersInRadius, player)
            end
        end
    end
    return playersInRadius
end

function xScript.GetNearestPart(options)
    options = options or {}
    local localCharacter = xScript.GetLocalCharacter()
    local localHRP = localCharacter and localCharacter:FindFirstChild("HumanoidRootPart")

    if not localHRP then warn("xScript.GetNearestPart: Local character or HumanoidRootPart not found."); return nil end

    local nearestPart = nil
    local minDistance = options.maxDistance or math.huge

    local excludedInstances = {}
    if options.excludePlayerCharacters ~= false then
        for _, p in ipairs(xScript.Services.Players:GetPlayers()) do
            if p.Character then table.insert(excludedInstances, p.Character) end
        end
    end
    if localCharacter then table.insert(excludedInstances, localCharacter) end
    
    local workspaceDescendants = xScript.Services.Workspace:GetDescendants()

    for _, descendant in ipairs(workspaceDescendants) do
        if not descendant:IsA("BasePart") then continue end

        local isExcluded = false
        for _, excludedChar in ipairs(excludedInstances) do
            if descendant:IsDescendantOf(excludedChar) then
                isExcluded = true
                break
            end
        end
        if isExcluded then continue end

        if not options.includeInvisible and (descendant.Transparency >= 0.999 or (descendant:FindFirstChildOfClass("SpecialMesh") and descendant.Mesh.Transparency >= 0.999) or (descendant.Class == "MeshPart" and descendant.MeshTransparency >= 0.999)) then
            continue
        end

        if not options.includeUncollidable and not descendant.CanCollide then
            continue
        end

        if options.classNames then
            local matchedClass = false
            if typeof(options.classNames) == "string" then
                if descendant:IsA(options.classNames) then matchedClass = true end
            elseif typeof(options.classNames) == "table" then
                for _, className in ipairs(options.classNames) do
                    if descendant:IsA(className) then
                        matchedClass = true
                        break
                    end
                end
            end
            if not matchedClass then continue end
        end

        if options.nameFilter then
            local matchedName = false
            local partName = string.lower(descendant.Name)
            local function checkName(filterPattern)
                local filter = string.lower(filterPattern)
                local lenFilter = string.len(filter)
                if lenFilter == 0 then return false end

                if string.sub(filter, 1, 1) == "%" then
                    if lenFilter > 1 and string.sub(partName, -(lenFilter - 1)) == string.sub(filter, 2) then return true end
                elseif string.sub(filter, -1) == "%" then
                    if lenFilter > 1 and string.sub(partName, 1, lenFilter - 1) == string.sub(filter, 1, lenFilter - 1) then return true end
                else
                    if partName == filter then return true end
                    if partName:find(filter, 1, true) then return true end
                end
                return false
            end

            if typeof(options.nameFilter) == "string" then
                if checkName(options.nameFilter) then matchedName = true end
            elseif typeof(options.nameFilter) == "table" then
                for _, namePattern in ipairs(options.nameFilter) do
                    if checkName(namePattern) then matchedName = true; break end
                end
            end
            if not matchedName then continue end
        end

        if options.filterFunction and not options.filterFunction(descendant) then
            continue
        end

        local distance = (localHRP.Position - descendant.Position).magnitude
        if distance < minDistance then
            minDistance = distance
            nearestPart = descendant
        end
    end
    return nearestPart
end

function xScript.GetAllPartsInRadius(centerReference, radius, options)
    options = options or {}
    local partsInRadius = {}

    local centerPos = xScript.GetPosition(centerReference)
    if not centerPos then warn("xScript.GetAllPartsInRadius: Invalid center reference provided (not Vector3, CFrame, BasePart or Model)."); return partsInRadius end
    if not typeof(radius) == "number" or radius < 0 then warn("xScript.GetAllPartsInRadius: Invalid radius provided (must be a non-negative number)."); return partsInRadius end

    local excludedInstances = {}
    if options.excludePlayerCharacters ~= false then
        for _, p in ipairs(xScript.Services.Players:GetPlayers()) do
            if p.Character then table.insert(excludedInstances, p.Character) end
        end
    end
    local localCharacter = xScript.GetLocalCharacter()
    if localCharacter then table.insert(excludedInstances, localCharacter) end
    
    local workspaceDescendants = xScript.Services.Workspace:GetDescendants()

    for _, descendant in ipairs(workspaceDescendants) do
        if not descendant:IsA("BasePart") then continue end

        local isExcluded = false
        for _, excludedChar in ipairs(excludedInstances) do
            if descendant:IsDescendantOf(excludedChar) then
                isExcluded = true
                break
            end
        end
        if isExcluded then continue end

        if not options.includeInvisible and (descendant.Transparency >= 0.999 or (descendant:FindFirstChildOfClass("SpecialMesh") and descendant.Mesh.Transparency >= 0.999) or (descendant.Class == "MeshPart" and descendant.MeshTransparency >= 0.999)) then
            continue
        end

        if not options.includeUncollidable and not descendant.CanCollide then
            continue
        end

        if options.classNames then
            local matchedClass = false
            if typeof(options.classNames) == "string" then
                if descendant:IsA(options.classNames) then matchedClass = true end
            elseif typeof(options.classNames) == "table" then
                for _, className in ipairs(options.classNames) do
                    if descendant:IsA(className) then
                        matchedClass = true
                        break
                    end
                end
            end
            if not matchedClass then continue end
        end

        if options.nameFilter then
            local matchedName = false
            local partName = string.lower(descendant.Name)
            local function checkName(filterPattern)
                local filter = string.lower(filterPattern)
                local lenFilter = string.len(filter)
                if lenFilter == 0 then return false end

                if string.sub(filter, 1, 1) == "%" then
                    if lenFilter > 1 and string.sub(partName, -(lenFilter - 1)) == string.sub(filter, 2) then return true end
                elseif string.sub(filter, -1) == "%" then
                    if lenFilter > 1 and string.sub(partName, 1, lenFilter - 1) == string.sub(filter, 1, lenFilter - 1) then return true end
                else
                    if partName == filter then return true end
                    if partName:find(filter, 1, true) then return true end
                end
                return false
            end

            if typeof(options.nameFilter) == "string" then
                if checkName(options.nameFilter) then matchedName = true end
            elseif typeof(options.nameFilter) == "table" then
                for _, namePattern in ipairs(options.nameFilter) do
                    if checkName(namePattern) then matchedName = true; break end
                end
            end
            if not matchedName then continue end
        end

        if options.filterFunction and not options.filterFunction(descendant) then
            continue
        end
        
        local distance = (centerPos - descendant.Position).magnitude
        if distance <= radius then
            table.insert(partsInRadius, descendant)
        end
    end
    return partsInRadius
end

function xScript.FindInstance(name, parent, recursive, classHint)
    if not parent then warn("xScript.FindInstance: Parent is nil."); return nil end
    if not typeof(name) == "string" then warn("xScript.FindInstance: Invalid name provided (must be string)."); return nil end

    local lowerName = string.lower(name)
    local instancesToSearch = recursive and parent:GetDescendants() or parent:GetChildren()

    for _, instance in ipairs(instancesToSearch) do
        if classHint then
            if typeof(classHint) == "string" then
                if not instance:IsA(classHint) then continue end
            elseif typeof(classHint) == "table" then
                local matchedClass = false
                for _, hint in ipairs(classHint) do
                    if instance:IsA(hint) then matchedClass = true; break end
                end
                if not matchedClass then continue end
            else
                warn("xScript.FindInstance: Invalid classHint type (must be string or table).")
            end
        end
        
        if string.lower(instance.Name) == lowerName then
            return instance
        end
    end
    return nil
end

function xScript.FindAllInstances(name, parent, recursive, classHint)
    local foundInstances = {}
    if not parent then warn("xScript.FindAllInstances: Parent is nil."); return foundInstances end
    if not typeof(name) == "string" then warn("xScript.FindAllInstances: Invalid name provided (must be string)."); return foundInstances end

    local lowerName = string.lower(name)
    local instancesToSearch = recursive and parent:GetDescendants() or parent:GetChildren()

    for _, instance in ipairs(instancesToSearch) do
        if classHint then
            if typeof(classHint) == "string" then
                if not instance:IsA(classHint) then continue end
            elseif typeof(classHint) == "table" then
                local matchedClass = false
                for _, hint in ipairs(classHint) do
                    if instance:IsA(hint) then matchedClass = true; break end
                end
                if not matchedClass then continue end
            else
                warn("xScript.FindAllInstances: Invalid classHint type (must be string or table).")
            end
        end
        
        if string.lower(instance.Name) == lowerName then
            table.insert(foundInstances, instance)
        end
    end
    return foundInstances
end

function xScript.GetCharacterHeadPosition(playerName)
    local char = xScript.GetCharacter(playerName)
    if not char then warn("xScript.GetCharacterHeadPosition: Character not found for player: "..tostring(playerName)); return nil end
    local head = char:FindFirstChild("Head")
    if not head then warn("xScript.GetCharacterHeadPosition: Head part not found for player: "..tostring(playerName)); return nil end
    return head.Position
end

function xScript.GetPartUnderPlayer(playerName)
    local char = xScript.GetCharacter(playerName)
    if not char then warn("xScript.GetPartUnderPlayer: Character not found for player: "..tostring(playerName)); return nil end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then warn("xScript.GetPartUnderPlayer: HumanoidRootPart not found for player: "..tostring(playerName)); return nil end

    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Exclude
    raycastParams.FilterDescendantsInstances = {char}
    
    local rayOrigin = hrp.Position
    local rayDirection = Vector3.new(0, -hrp.Size.Y/2 - 1, 0)
    
    local raycastResult = xScript.Services.Workspace:Raycast(rayOrigin, rayDirection, raycastParams)
    
    if raycastResult then
        return raycastResult.Instance
    end
    return nil
end

function xScript.GetMousePosition()
    local uis = xScript.Services.UserInputService
    if uis then
        return uis:GetMouseLocation()
    end
    warn("xScript.GetMousePosition: UserInputService not available.")
    return Vector2.new(0,0)
end

function xScript.RepeatUntilFind(instanceName, parent, recursive, timeoutSeconds, pollingRate)
    if not typeof(instanceName) == "string" or string.len(instanceName) == 0 then warn("xScript.RepeatUntilFind: Invalid instanceName (must be non-empty string)."); return nil end
    if not parent then warn("xScript.RepeatUntilFind: Invalid parent (must be an Instance)."); return nil end
    recursive = recursive == true
    timeoutSeconds = typeof(timeoutSeconds) == "number" and timeoutSeconds > 0 and timeoutSeconds or 10
    pollingRate = typeof(pollingRate) == "number" and pollingRate > 0 and pollingRate or 0.05

    local startTime = tick()
    while tick() - startTime < timeoutSeconds do
        local foundInstance = recursive and parent:FindFirstChild(instanceName, true) or parent:FindFirstChild(instanceName)
        if foundInstance then
            return foundInstance
        end
        task.wait(pollingRate)
    end
    warn("xScript.RepeatUntilFind: Instance '"..instanceName.."' not found within "..timeoutSeconds.." seconds in "..tostring(parent.Name).." (recursive: "..tostring(recursive)..").")
    return nil
end

function xScript.WaitForProperty(instance, propertyName, targetValue, timeoutSeconds)
    if not instance then warn("xScript.WaitForProperty: Invalid instance (nil)."); return false end
    if not typeof(propertyName) == "string" or string.len(propertyName) == 0 then warn("xScript.WaitForProperty: Invalid propertyName (must be non-empty string)."); return false end
    timeoutSeconds = typeof(timeoutSeconds) == "number" and timeoutSeconds > 0 and timeoutSeconds or 5

    local startTime = tick()
    while tick() - startTime < timeoutSeconds do
        local success, currentVal = pcall(function() return instance[propertyName] end)
        if not success then
            warn("xScript.WaitForProperty: Error accessing property '"..propertyName.."' on "..tostring(instance)..": "..tostring(currentVal))
            return false
        end

        if currentVal == targetValue then
            return true
        end
        task.wait(0.05)
    end
    warn("xScript.WaitForProperty: Property '"..propertyName.."' on "..tostring(instance.Name).." did not reach target value within "..timeoutSeconds.." seconds (current: "..tostring(instance[propertyName])..", target: "..tostring(targetValue)..").")
    return false
end

function xScript.SetClipboard(text)
    if typeof(setclipboard) == "function" then
        setclipboard(text)
    else
        warn("xScript.SetClipboard: 'setclipboard' function is not available in this environment.")
    end
end

function xScript.SetLocalPlayerPosition(vector3)
    local localCharacter = xScript.GetLocalCharacter()
    if not localCharacter then warn("xScript.SetLocalPlayerPosition: Local character not found.") return end
    local hrp = localCharacter:FindFirstChild("HumanoidRootPart")
    if hrp then
        hrp.CFrame = CFrame.new(vector3)
    else
        warn("xScript.SetLocalPlayerPosition: Local HumanoidRootPart not found.")
    end
end

function xScript.SetPosition(targetObject, vector3)
    if not targetObject then
        warn("xScript.SetPosition: Target object is nil.")
        return
    end

    local newCFrame = CFrame.new(vector3)

    if targetObject:IsA("BasePart") then
        targetObject.CFrame = newCFrame
    elseif targetObject:IsA("Model") and targetObject.PrimaryPart then
        targetObject:SetPrimaryPartCFrame(newCFrame)
    else
        warn("xScript.SetPosition: Unmovable object type or missing PrimaryPart for Model: ", typeof(targetObject), targetObject.Name or "Unnamed")
    end
end

function xScript.SetHealth(number)
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if localHumanoid then
        localHumanoid.Health = number
    else
        warn("xScript.SetHealth: Local character Humanoid not found.")
    end
end

function xScript.SetWalkspeed(number, bypass)
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if not localHumanoid then warn("xScript.SetWalkspeed: Local character Humanoid not found.") return end
    
    _bypassWalkspeedActive = bypass == true

    if _bypassWalkspeedActive then
        localHumanoid.WalkSpeed = 16
    else
        localHumanoid.WalkSpeed = number
    end
end

function xScript.SetJumpPower(number, bypass)
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if not localHumanoid then warn("xScript.SetJumpPower: Local character Humanoid not found.") return end

    _bypassJumpPowerActive = bypass == true

    if _bypassJumpPowerActive then
        localHumanoid.JumpPower = 50
    else
        localHumanoid.JumpPower = number
    end
end

function xScript.SetGravity(number)
    if typeof(number) == "number" then
        xScript.Services.Workspace.Gravity = number
    else
        warn("xScript.SetGravity: Invalid number provided for gravity.")
    end
end

function xScript.SetParent(instance, newParent)
    if instance then
        instance.Parent = newParent
        return true
    else
        warn("xScript.SetParent: Invalid instance provided.")
        return false
    end
end

local _oldFogValues = nil

function xScript.ToggleFog(enable)
    local lighting = xScript.Services.Lighting
    if not lighting then warn("xScript.ToggleFog: Lighting service not found."); return end

    if enable then
        if _oldFogValues == nil then
            _oldFogValues = {
                Color = lighting.FogColor,
                Start = lighting.FogStart,
                End = lighting.FogEnd,
            }
        end
        lighting.FogStart = 0
        lighting.FogEnd = 0
    else
        if _oldFogValues ~= nil then
            lighting.FogColor = _oldFogValues.Color
            lighting.FogStart = _oldFogValues.Start
            lighting.FogEnd = _oldFogValues.End
            _oldFogValues = nil
        else
            lighting.FogStart = 0
            lighting.FogEnd = 100000
            lighting.FogColor = Color3.new(0,0,0)
        end
    end
end

function xScript.MouseMove(x, y)
    if typeof(mouse_move) == "function" then
        pcall(mouse_move, x, y)
    else
        warn("xScript.MouseMove: 'mouse_move' function is not available in this executor. Mouse movement may not be possible.")
    end
end

function xScript.MouseDown(button)
    if button == Enum.UserInputType.MouseButton1 then
        if typeof(mouse1down) == "function" then pcall(mouse1down) else warn("xScript.MouseDown: 'mouse1down' not available.") end
    elseif button == Enum.UserInputType.MouseButton2 then
        if typeof(mouse2down) == "function" then pcall(mouse2down) else warn("xScript.MouseDown: 'mouse2down' not available.") end
    elseif button == Enum.UserInputType.MouseButton3 then
        if typeof(mouse3down) == "function" then pcall(mouse3down) else warn("xScript.MouseDown: 'mouse3down' not available.") end
    else
        warn("xScript.MouseDown: Invalid button type. Use Enum.UserInputType.MouseButton1/2/3.")
    end
end

function xScript.MouseUp(button)
    if button == Enum.UserInputType.MouseButton1 then
        if typeof(mouse1up) == "function" then pcall(mouse1up) else warn("xScript.MouseUp: 'mouse1up' not available.") end
    elseif button == Enum.UserInputType.MouseButton2 then
        if typeof(mouse2up) == "function" then pcall(mouse2up) else warn("xScript.MouseUp: 'mouse2up' not available.") end
    elseif button == Enum.UserInputType.MouseButton3 then
        if typeof(mouse3up) == "function" then pcall(mouse3up) else warn("xScript.MouseUp: 'mouse3up' not available.") end
    else
        warn("xScript.MouseUp: Invalid button type. Use Enum.UserInputType.MouseButton1/2/3.")
    end
end

function xScript.MouseClick(button)
    if button == Enum.UserInputType.MouseButton1 then
        if typeof(mouse1click) == "function" then pcall(mouse1click) else warn("xScript.MouseClick: 'mouse1click' not available.") end
    elseif button == Enum.UserInputType.MouseButton2 then
        if typeof(mouse2click) == "function" then pcall(mouse2click) else warn("xScript.MouseClick: 'mouse2click' not available.") end
    elseif button == Enum.UserInputType.MouseButton3 then
        if typeof(mouse3click) == "function" then pcall(mouse3click) else warn("xScript.MouseClick: 'mouse3click' not available.") end
    else
        warn("xScript.MouseClick: Invalid button type. Use Enum.UserInputType.MouseButton1/2/3.")
    end
end

function xScript.SimulateKey(keyCode, duration)
    if not typeof(keyCode) == "userdata" or not (keyCode:IsA("EnumItem") and keyCode.EnumType == Enum.EnumType.KeyCode) then 
        warn("xScript.SimulateKey: Invalid keyCode provided (must be an Enum.KeyCode)."); return false 
    end
    duration = typeof(duration) == "number" and duration > 0 and duration or 0.1

    local keyFunc = getfenv().keypress or getfenv().key_press

    if keyFunc then
        local success, err = pcall(keyFunc, keyCode, duration)
        if not success then warn("xScript.SimulateKey: Executor key function error: "..tostring(err)); return false end
        return true
    else
        warn("xScript.SimulateKey: No executor-specific key press function found ('keypress' or 'key_press'). This function requires executor support.")
        return false
    end
end

function xScript.Create(class, name, parent)
    local newInstance = Instance.new(class)
    newInstance.Name = name
    if parent then
        newInstance.Parent = parent
    end
    return newInstance
end

function xScript.PlayerList()
    local list = {}
    for _, player in ipairs(xScript.Services.Players:GetPlayers()) do
        table.insert(list, player.Name)
    end
    return list
end

function xScript.Backpack()
    local player = xScript.GetLocalPlayer()
    if player then
        return player.Backpack
    end
    warn("xScript.Backpack: Local player not found.")
    return nil
end

function xScript.EquipTool(tool)
    local localPlayer = xScript.GetLocalPlayer()
    if not localPlayer then warn("xScript.EquipTool: Local player not found.") return end
    local targetTool = (typeof(tool) == "string" and localPlayer.Backpack:FindFirstChild(tool)) or tool
    
    if targetTool and targetTool:IsA("Tool") then
        local human = localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Humanoid")
        if human then
            human:EquipTool(targetTool)
        else
            warn("xScript.EquipTool: Local character Humanoid not found.")
        end
    else
        warn("xScript.EquipTool: Tool '" .. tostring(tool) .. "' not found or is not a Tool instance.")
    end
end

function xScript.UnequipTools()
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if localHumanoid then 
        localHumanoid:UnequipTools()
    else
        warn("xScript.UnequipTools: Local character Humanoid not found.")
    end
end

function xScript.CharacterTools(enable)
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    local localPlayer = xScript.GetLocalPlayer()
    if not localHumanoid or not localPlayer then warn("xScript.CharacterTools: Local character or Humanoid not found.") return end

    if enable then
        for _, tool in ipairs(localPlayer.Backpack:GetChildren()) do
            if tool:IsA("Tool") then
                localHumanoid:EquipTool(tool)
            end
        end
    else
        localHumanoid:UnequipTools()
    end
end

local _xrayStates = {}
function xScript.ToggleXRay(enable, targetTransparency, excludeCharacters)
    targetTransparency = targetTransparency or 0.6
    if not (typeof(targetTransparency) == "number" and targetTransparency >= 0 and targetTransparency <= 1) then
        warn("xScript.ToggleXRay: Invalid targetTransparency provided. Must be a number between 0 and 1. Defaulting to 0.6.")
        targetTransparency = 0.6
    end
    excludeCharacters = excludeCharacters == nil and true or excludeCharacters

    local workspaceParts = xScript.Services.Workspace:GetDescendantsOfType("BasePart")
    local playersChars = {}
    if excludeCharacters then
        for _, player in ipairs(xScript.Services.Players:GetPlayers()) do
            if player.Character then table.insert(playersChars, player.Character) end
        end
    end

    for _, part in ipairs(workspaceParts) do
        local isCharacterPart = false
        if excludeCharacters then
            for _, char in ipairs(playersChars) do
                if part:IsDescendantOf(char) then
                    isCharacterPart = true
                    break
                end
            end
        end
        if isCharacterPart then continue end

        if enable then
            if not _xrayStates[part] then
                _xrayStates[part] = {Transparency = part.Transparency, Material = part.Material, Collide = part.CanCollide}
            end
            part.Transparency = targetTransparency
        else
            if _xrayStates[part] then
                part.Transparency = _xrayStates[part].Transparency
                _xrayStates[part] = nil
            end
        end
    end
    if not enable then _xrayStates = {} end
end

local _frozenState = false
local _oldWS, _oldJP, _oldPlatformStand, _oldAnchor = nil, nil, nil, nil
function xScript.FreezeLocalCharacter(enable)
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    local localHRP = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChild("HumanoidRootPart")
    if not localHumanoid then warn("xScript.FreezeLocalCharacter: Local Humanoid not found."); return end
    if not localHRP then warn("xScript.FreezeLocalCharacter: Local HumanoidRootPart not found."); return end

    if enable and not _frozenState then
        _frozenState = true
        _oldWS = localHumanoid.WalkSpeed
        _oldJP = localHumanoid.JumpPower
        _oldPlatformStand = localHumanoid.PlatformStand
        _oldAnchor = localHRP.Anchored

        localHumanoid.WalkSpeed = 0
        localHumanoid.JumpPower = 0
        localHumanoid.PlatformStand = true
        localHRP.Anchored = true
    elseif not enable and _frozenState then
        _frozenState = false
        if _oldWS ~= nil then localHumanoid.WalkSpeed = _oldWS end
        if _oldJP ~= nil then localHumanoid.JumpPower = _oldJP end
        if _oldPlatformStand ~= nil then localHumanoid.PlatformStand = _oldPlatformStand end
        if _oldAnchor ~= nil then localHRP.Anchored = _oldAnchor end
        
        _oldWS, _oldJP, _oldPlatformStand, _oldAnchor = nil, nil, nil, nil
    end
end

function xScript.DeleteLocalInstance(instance)
    if instance and instance.Parent then
        instance:Destroy()
        return true
    else
        warn("xScript.DeleteLocalInstance: Invalid instance provided or instance has no parent to destroy from.")
        return false
    end
end

local _flying = false
local _flyConn = nil
local _oldMoveSpeed = 0
function xScript.Fly(enable, speed)
    local localCharacter = xScript.GetLocalCharacter()
    if not localCharacter then warn("xScript.Fly: Local character not found.") return end
    local localHumanoid = localCharacter:FindFirstChildOfClass("Humanoid")
    local localHRP = localCharacter:FindFirstChild("HumanoidRootPart")
    
    if not localHumanoid or not localHRP then warn("xScript.Fly: Local character Humanoid or HumanoidRootPart not found.") return end

    local currentCam = xScript.Services.Workspace.CurrentCamera

    if enable and not _flying then
        _flying = true
        _oldMoveSpeed = localHumanoid.WalkSpeed
        localHumanoid.WalkSpeed = speed or 100

        localHumanoid.PlatformStand = true

        _flyConn = xScript.Services.RunService.RenderStepped:Connect(function()
            if not _flying then return end

            local cframe = currentCam.CFrame
            local direction = Vector3.new()
            local uis = xScript.Services.UserInputService

            if uis:IsKeyDown(Enum.KeyCode.W) then direction += cframe.LookVector end
            if uis:IsKeyDown(Enum.KeyCode.S) then direction -= cframe.LookVector end
            if uis:IsKeyDown(Enum.KeyCode.A) then direction -= cframe.RightVector end
            if uis:IsKeyDown(Enum.KeyCode.D) then direction += cframe.RightVector end
            if uis:IsKeyDown(Enum.KeyCode.E) then direction += Vector3.new(0,1,0) end
            if uis:IsKeyDown(Enum.KeyCode.Q) then direction -= Vector3.new(0,1,0) end

            direction = direction.Unit * (localHumanoid.WalkSpeed / 60)
            
            if direction.magnitude > 0 then 
                localHRP.CFrame += CFrame.new(direction.X, direction.Y, direction.Z)
            end
        end)
    elseif not enable and _flying then
        _flying = false
        if _flyConn then
            _flyConn:Disconnect()
            _flyConn = nil
        end
        localHumanoid.WalkSpeed = _oldMoveSpeed
        localHumanoid.PlatformStand = false
        _oldMoveSpeed = 0
    end
end

local _infiniteJumpEnabled = false
local _jumpConn = nil
function xScript.InfiniteJump(enable)
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if not localHumanoid then warn("xScript.InfiniteJump: Local character Humanoid not found.") return end

    if enable and not _infiniteJumpEnabled then
        _infiniteJumpEnabled = true
        _jumpConn = localHumanoid.FreeFalling:Connect(function(active)
            if active and _infiniteJumpEnabled and localHumanoid.Jump and not localHumanoid.Sit then
                localHumanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end)
    elseif not enable and _infiniteJumpEnabled then
        _infiniteJumpEnabled = false
        if _jumpConn then
            _jumpConn:Disconnect()
            _jumpConn = nil
        end
    end
end

function xScript.ForceJump()
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if localHumanoid then
        localHumanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    else
        warn("xScript.ForceJump: Local character Humanoid not found.")
    end
end

function xScript.RespawnLocalPlayer()
    local localPlayer = xScript.GetLocalPlayer()
    if localPlayer then
        localPlayer:LoadCharacter()
        return true
    else
        warn("xScript.RespawnLocalPlayer: Local player not found.")
        return false
    end
end

function xScript.Teleport(placeId)
    local ts = game:GetService("TeleportService")
    ts:Teleport(placeId)
end

function xScript.Rejoin(sameserver)
    local ts = game:GetService("TeleportService")
    if sameserver then
        ts:TeleportToPlaceInstance(game.PlaceId,game.JobId)
    else
        ts:Teleport(game.PlaceId)
    end
end

function xScript.Time()
    return os.date("!*t")
end

function xScript.Kick(message)
    game.Players.LocalPlayer:Kick(message)
end

function xScript.Kill()
    local localHumanoid = xScript.GetLocalCharacter() and xScript.GetLocalCharacter():FindFirstChildOfClass("Humanoid")
    if localHumanoid then
        localHumanoid.Health = 0
    else
        warn("xScript.Kill: Local character Humanoid not found.")
    end
end

function xScript.ExecutorName()
    if typeof(getexecutorname) == "function" then
        return getexecutorname()
    else
        warn("xScript.ExecutorName: 'getexecutorname' function is not available in this environment.")
        return "Unknown"
    end
end

function xScript.FireButton(button)
    if not button then warn("xScript.FireButton: Invalid button object provided (nil).") return end
    if not button:IsA("GuiButton") then warn("xScript.FireButton: Provided object is not a GuiButton: "..tostring(button.Name)); return end
    if typeof(getconnections) ~= "function" then warn("xScript.FireButton: 'getconnections' function is not available.") return end

    for i,v in pairs(getconnections(button.MouseButton1Click)) do pcall(v.Fire, v) end
    for i,v in pairs(getconnections(button.MouseButton1Down)) do pcall(v.Fire, v) end
    for i,v in pairs(getconnections(button.MouseButton2Click)) do pcall(v.Fire, v) end
    for i,v in pairs(getconnections(button.MouseButton2Down)) do pcall(v.Fire, v) end
end

function xScript.FireRemote(remoteEvent, ...)
    if remoteEvent and remoteEvent:IsA("RemoteEvent") then
        remoteEvent:FireServer(...)
    else
        warn("xScript.FireRemote: Invalid RemoteEvent provided.")
    end
end

function xScript.InvokeRemote(remoteFunction, ...)
    if remoteFunction and remoteFunction:IsA("RemoteFunction") then
        return remoteFunction:InvokeServer(...)
    else
        warn("xScript.InvokeRemote: Invalid RemoteFunction provided.")
    end
    return nil
end

function xScript.TeleportToNearestPlayer(maxDistance)
    local nearestPlayer = xScript.GetNearestPlayer(maxDistance)
    if nearestPlayer and nearestPlayer.Character and nearestPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local targetPosition = nearestPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 5, 0)
        xScript.SetLocalPlayerPosition(targetPosition)
        return true
    end
    return false
end

function xScript.ClickDetector(part)
    if not part or not part:IsA("BasePart") then warn("xScript.ClickDetector: Invalid BasePart provided."); return false end
    local clickDetector = part:FindFirstChildOfClass("ClickDetector")
    if clickDetector then
        clickDetector:MouseClick()
        return true
    end
    warn("xScript.ClickDetector: ClickDetector not found in part: "..tostring(part.Name))
    return false
end

function xScript.FireProximityPrompt(proximityPrompt)
    if proximityPrompt and proximityPrompt:IsA("ProximityPrompt") then
        proximityPrompt:Triggered()
        return true
    end
    warn("xScript.FireProximityPrompt: Invalid ProximityPrompt provided.")
    return false
end

function xScript.ClearChildren(instance)
    if not instance then warn("xScript.ClearChildren: Instance is nil."); return end
    for _, child in ipairs(instance:GetChildren()) do
        child:Destroy()
    end
end

function xScript.HighlightPart(part, color, duration)
    if not part or not part:IsA("BasePart") then warn("xScript.HighlightPart: Invalid BasePart provided."); return nil end

    local existingSelectionBox = part:FindFirstChild("xScriptHighlightSelectionBox")
    if existingSelectionBox then existingSelectionBox:Destroy() end

    local selectionBox = Instance.new("SelectionBox", part)
    selectionBox.LineThickness = 0.2
    selectionBox.Color3 = color or Color3.fromRGB(255, 255, 0)
    selectionBox.Transparency = 0
    selectionBox.Name = "xScriptHighlightSelectionBox"

    if duration and typeof(duration) == "number" and duration > 0 then
        task.delay(duration, function()
            if selectionBox and selectionBox.Parent == part and selectionBox.Name == "xScriptHighlightSelectionBox" then
                selectionBox:Destroy()
            end
        end)
    end
    return selectionBox
end

local _activeESPTags = {}
function xScript.AttachESP(targetPart, type, options)
    options = options or {}
    local partToAttach = targetPart
    if not partToAttach then warn("xScript.AttachESP: No target part provided."); return nil end

    if type == "Billboard" then
        local adornee = partToAttach:IsA("Humanoid") and partToAttach.Parent:FindFirstChild("Head") or partToAttach
        if not adornee then warn("xScript.AttachESP: No valid adornee found for Billboard type ESP."); return nil end

        local existingBillboard = _activeESPTags[targetPart] and _activeESPTags[targetPart].Billboard
        if existingBillboard and existingBillboard.Parent then existingBillboard:Destroy() end

        local billboard = Instance.new("BillboardGui")
        billboard.Adornee = adornee
        billboard.Size = UDim2.new(0, 150, 0, 50)
        billboard.AlwaysOnTop = true
        billboard.LightInfluence = 0
        billboard.ZIndex = 10
        billboard.ExtentsOffset = Vector3.new(0, (partToAttach.Size.Y or 2)/2 + 2, 0)

        local textLabel = Instance.new("TextLabel")
        textLabel.Size = UDim2.new(1,0,1,0)
        textLabel.BackgroundTransparency = 1
        textLabel.TextScaled = true
        textLabel.Font = Enum.Font.SourceSansBold
        textLabel.TextColor3 = options.Color or Color3.fromRGB(255, 255, 0)
        textLabel.TextStrokeTransparency = 0
        textLabel.Text = options.Text or (targetPart.Parent and targetPart.Parent.Name or targetPart.Name)
        textLabel.Parent = billboard

        billboard.Parent = xScript.Services.CoreGui or xScript.Services.Players.LocalPlayer:FindFirstChildOfClass("PlayerGui")
        if not billboard.Parent then warn("xScript.AttachESP: Failed to find a suitable parent for BillboardGui (CoreGui/PlayerGui).") end

        _activeESPTags[targetPart] = {Billboard = billboard, Adornee = adornee}

        if options.Duration and typeof(options.Duration) == "number" and options.Duration > 0 then
            task.delay(options.Duration, function()
                if billboard and billboard.Parent and _activeESPTags[targetPart] then
                    billboard:Destroy()
                    _activeESPTags[targetPart] = nil
                end
            end)
        end
        return billboard

    elseif type == "Highlight" then
        return xScript.HighlightPart(targetPart, options.Color, options.Duration)

    else
        warn("xScript.AttachESP: Invalid ESP type provided. Supported: 'Billboard', 'Highlight'.")
    end
    return nil
end

function xScript.DetachESP(targetPart)
    if _activeESPTags[targetPart] then
        if _activeESPTags[targetPart].Billboard then
            _activeESPTags[targetPart].Billboard:Destroy()
        end
        _activeESPTags[targetPart] = nil
        return true
    end
    local existingHighlight = targetPart and targetPart:FindFirstChild("xScriptHighlightSelectionBox")
    if existingHighlight then
        existingHighlight:Destroy()
        return true
    end
    return false
end

local _guiClickConnections = {}
function xScript.AttachClickEvent(guiObject, callbackFunction)
    if not guiObject or not guiObject:IsA("GuiObject") then warn("xScript.AttachClickEvent: Invalid GuiObject provided."); return nil end
    if not typeof(callbackFunction) == "function" then warn("xScript.AttachClickEvent: Invalid callbackFunction provided (must be a function)."); return nil end

    local connection = nil
    if guiObject:IsA("TextButton") or guiObject:IsA("ImageButton") then
        connection = guiObject.MouseButton1Click:Connect(function()
            pcall(callbackFunction, guiObject)
        end)
    elseif guiObject:IsA("Frame") or guiObject:IsA("ImageLabel") or guiObject:IsA("TextLabel") then
        connection = guiObject.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                pcall(callbackFunction, guiObject)
            end
        end)
    end

    if connection then
        if not _guiClickConnections[guiObject] then _guiClickConnections[guiObject] = {} end
        table.insert(_guiClickConnections[guiObject], connection)
        return connection
    else
        warn("xScript.AttachClickEvent: Could not attach a suitable click event to "..tostring(guiObject.Name)..
              ". Supported: TextButton, ImageButton, (limited Frame/Label via InputBegan).")
        return nil
    end
end

function xScript.DetachClickEvent(guiObjectOrConnection)
    if typeof(guiObjectOrConnection) == "userdata" and guiObjectOrConnection:IsA("RBXScriptConnection") then
        if not guiObjectOrConnection.Connected then return false end
        guiObjectOrConnection:Disconnect()
        return true
    elseif typeof(guiObjectOrConnection) == "userdata" and guiObjectOrConnection:IsA("GuiObject") then
        if _guiClickConnections[guiObjectOrConnection] then
            for _, conn in ipairs(_guiClickConnections[guiObjectOrConnection]) do
                if conn.Connected then conn:Disconnect() end
            end
            _guiClickConnections[guiObjectOrConnection] = nil
            return true
        end
    end
    warn("xScript.DetachClickEvent: Invalid argument (expected GuiObject or RBXScriptConnection) or no active connections found.")
    return false
end

function xScript.CreateWebSocket(url)
    if not typeof(url) == "string" or string.len(url) == 0 then
        warn("xScript.CreateWebSocket: Invalid URL provided (must be non-empty string).")
        return nil
    end

    local WebSocketAPI = getfenv().WebSocket or nil

    if not WebSocketAPI then
        warn("xScript.CreateWebSocket: 'WebSocket' global/module not found in this executor environment.")
        warn("   WebSocket functionality requires specific executor support.")
        return nil
    end

    local WebSocketClient = {}
    WebSocketClient.OnMessage = Instance.new("BindableEvent")
    WebSocketClient.OnClose = Instance.new("BindableEvent")
    WebSocketClient.OnError = Instance.new("BindableEvent")

    WebSocketClient.Connection = nil

    local success, ws = pcall(function()
        if typeof(WebSocketAPI.Connect) == "function" then
            return WebSocketAPI.Connect(url)
        elseif typeof(WebSocketAPI) == "function" then
            return WebSocketAPI(url)
        end
        return nil
    end)

    if not success or not ws then
        warn("xScript.CreateWebSocket: Failed to establish WebSocket connection for URL: "..url..". Error: "..tostring(ws))
        WebSocketClient.OnError:Fire("Failed to establish WebSocket connection: "..tostring(ws))
        return nil
    end

    WebSocketClient.Connection = ws

    if ws.OnMessage and typeof(ws.OnMessage.Connect) == "function" then
        ws.OnMessage:Connect(function(message, is_binary)
            WebSocketClient.OnMessage:Fire(message, is_binary)
        end)
    end

    if ws.OnClose and typeof(ws.OnClose.Connect) == "function" then
        ws.OnClose:Connect(function(code, reason)
            WebSocketClient.OnClose:Fire(code, reason)
            WebSocketClient.OnMessage:Destroy()
            WebSocketClient.OnClose:Destroy()
            WebSocketClient.OnError:Destroy()
            WebSocketClient.Connection = nil
        end)
    end

    if ws.OnError and typeof(ws.OnError.Connect) == "function" then
        ws.OnError:Connect(function(message)
            WebSocketClient.OnError:Fire(message)
        end)
    end

    function WebSocketClient:Send(data, is_binary)
        if self.Connection and typeof(self.Connection.Send) == "function" then
            local success, err = pcall(self.Connection.Send, self.Connection, data, is_binary)
            if not success then warn("xScript.WebSocketClient: Failed to send data: ", err) end
            return success
        else
            warn("xScript.WebSocketClient: Cannot send data, WebSocket connection is not active or Send method is missing.")
            return false
        end
    end

    function WebSocketClient:Close(code, reason)
        if self.Connection and typeof(self.Connection.Close) == "function" then
            local success, err = pcall(self.Connection.Close, self.Connection, code, reason)
            if not success then warn("xScript.WebSocketClient: Failed to close connection: ", err) end
            return success
        else
            warn("xScript.WebSocketClient: Cannot close connection, WebSocket connection is not active or Close method is missing.")
            return false
        end
    end

    print("xScript.CreateWebSocket: WebSocket client created for URL: "..url)
    return WebSocketClient
end

function xScript.SendWebhook(webhook, message)
    local data = { ["content"] = message }
    local newdata = game:GetService("HttpService"):JSONEncode(data)
    
    local headers = { ["content-type"] = "application/json" }
    
    local requestFunction = http_request or request or HttpPost or syn.request
    if not requestFunction then warn("xScript.SendWebhook: No HTTP request function found for this executor.") return end

    local success, err = pcall(function()
        requestFunction({Url = webhook, Body = newdata, Method = "POST", Headers = headers})
    end)
    if not success then warn("xScript.SendWebhook: Failed to send webhook -", err) end
end

function xScript.SendEmbed(webhook, data)
    local newdata = game:GetService("HttpService"):JSONEncode(data)
    local headers = { ["content-type"] = "application/json" }

    local requestFunction = http_request or request or HttpPost or syn.request
    if not requestFunction then warn("xScript.SendEmbed: No HTTP request function found for this executor.") return end

    local success, err = pcall(function()
        requestFunction({Url = webhook, Body = newdata, Method = "POST", Headers = headers})
    end)
    if not success then warn("xScript.SendEmbed: Failed to send embed -", err) end
end

print("[2/2] Loaded xScript.", "Version", xScript.Version)
return xScript
