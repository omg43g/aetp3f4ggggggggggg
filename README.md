------------------------------------------------->
---------//          CheatBlox            //-----|
--------//                               //------|
-------//      Made By Stefan#6965      //-------|
------//                               //--------|
-----//   Thanks to all supporters!   //---------|
------------------------------------------------->


repeat wait() until game and workspace and game:IsLoaded()
if getgenv().CheatBloxRunning then return end
getgenv().CheatBloxRunning = true
for i,v in pairs(getconnections(game:GetService("ScriptContext").Error)) do
    v:Disable()
end
for i,v in pairs(getconnections(game:GetService("LogService").MessageOut)) do
    --v:Disable()
end
local CheatBlox = game:GetObjects("rbxassetid://6580324035")[1]
wait()
local LP = game:GetService("Players").LocalPlayer
local lexer = loadstring(game:HttpGet("https://raw.githubusercontent.com/ScriptingStefan/CheatBlox/main/lexer_modded.lua",true))()
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Settings = {
    ["Enabled"] = true,
    ["Sounds"] = true
}
local InTransition = false
local Minimized = false
local Hidden = false
local MenuOpened = false
local ToClearFrame
local ToClearBox
local Icons = {
    ["RemoteEvent"] = "http://www.roblox.com/asset/?id=413369506",
    ["RemoteFunction"] = "http://www.roblox.com/asset/?id=413369623",
    ["Function"] = "http://www.roblox.com/asset/?id=13546048",
    ["Call"] = "http://www.roblox.com/asset/?id=74436763",
    ["LocalScript"] = "http://www.roblox.com/asset/?id=5168599557",
    ["ModuleScript"] = "http://www.roblox.com/asset/?id=5016313061",
    ["Value"] = "http://www.roblox.com/asset/?id=266753862"
}
for i,v in pairs(Icons) do
    game:GetService("ContentProvider"):Preload(v)
    wait()
end

---// Data of RemoteSpy //---

local ChangelogText = [[
Press F8 to Hide.
Right-click a logged remote, click "Send to Debug", then go to Debug tab to edit upvalues,constants,etc.
Some elements are obscured by the menu frame, so be sure to rehide it.

<b>Changelog:</b>


<b>*</b> Changed UX a bit.
<b>*</b> Bugfixes.



Made By <b>Stefan#6965</b>
Discord Code: NRYJj9STbq
]]
local IgnoredRemotes = {}
local SelectedRemote
local SelectedRemoteArgs
local SpiedScripts = {}
local IgnoredCalls = {}
local SelectedCall
local old_rc_frame
local LogFunction
local ValueToString
local GetType
local MakeCoolButton
local UpdateRemotes
local CreateButtonLog
local UpdateScripts
local backup
local backup2
local backup3

---// Support //---

local getcallingfunction = function(stack)
    return debug.getinfo(stack + 1).func or debug.getinfo(stack + 1)["function"] or debug.getinfo(stack + 1)["Function"]
end
local getscriptfunction = getscriptfunction or getscriptclosure
if not syn then syn = {} end
if not syn.crypt then syn.crypt = {} end
if not crypt then crypt = {} end
local getcontext = getcontext or syn.get_thread_identity or getthreadcontext
local setcontext = setcontext or syn.set_thread_identity or setthreadcontext
local toclipboard = syn.write_clipboard or setclipboard or toclipboard or write_clipboard
local is_synapse_function = is_synapse_function or isexecutorclosure
local ran_gen = syn.crypt.random or crypt.generatebytes or function() return tostring(math.random(1,100)) end
local decompile = function(...)
    local args1231 = {...}
    local ret
    pcall(function()
        ret = decompile(unpack(args1231))
    end)
    if type(ret) ~= "string" or #ret < 1 then
        return "Decompilation Error"
    end
    return ret
end

---// Tweens //---

local FastTween = TweenInfo.new(0.3,Enum.EasingStyle.Quart,Enum.EasingDirection.Out,0,false,0)
local TextClickTween = TweenInfo.new(0.1,Enum.EasingStyle.Linear,Enum.EasingDirection.Out,0,true,0)

---// Base Functions //---

local CanClick = function()
    if InTransition or Minimized or Hidden then
        return false
    end
    return true
end

local ScriptingBox = function(box)
    local placecolor = Color3.new(1,1,1)
    local placetext = ""
    pcall(function()
        placetext = box.PlaceholderText
        placecolor = box.PlaceholderColor3
    end)
    local label = Instance.new("TextLabel")
    label.ZIndex = box.ZIndex + 1
    label.AnchorPoint = box.AnchorPoint
    label.TextXAlignment = box.TextXAlignment
    label.TextWrapped = box.TextWrapped
    label.TextYAlignment = box.TextYAlignment
    label.BorderColor3 = box.BorderColor3
    label.BorderMode = box.BorderMode
    label.BorderSizePixel = box.BorderSizePixel
    label.Rotation = box.Rotation
    label.TextColor3 = Color3.new(1,1,1)
    label.Size = box.Size
    label.Position = box.Position
    label.TextSize = box.TextSize
    label.Name = box.Name.." Copy lol"
    label.TextScaled = box.TextScaled
    label.Font = box.Font
    label.BackgroundTransparency = box.BackgroundTransparency
    label.BackgroundColor3 = box.BackgroundColor3
    label.RichText = true
    label.TextTruncate = Enum.TextTruncate.AtEnd
    for i,v in pairs(box:GetChildren()) do
        v:Clone().Parent = label
    end
    box.BackgroundTransparency = 1
    box.TextTransparency = 1
    box.TextStrokeTransparency = 1
    local colors = {
        ["keyword"] = "rgb(240, 40, 10)",
        ["number"] = "rgb(255, 255, 0)",
        ["iden"] = "rgb(255, 255, 255)",
        ["string"] = "rgb(0, 255, 0)",
        ["builtin"] = "rgb(0, 255, 255)",
        ["comment"] = "rgb(80, 80, 80)"
    }

    local bold = {
        ["builtin"] = true
    }
    local change
    change = function()
        label.ZIndex = box.ZIndex + 1
        label.Text = ""
        local ok = box.Text
        if ok == "" or ok == " " or not ok then
            if not placetext or placetext == "" then
                return
            else
                ok = placetext
            end
        end
        if box:IsA("TextBox") and box.SelectionStart ~= -1 and type(box.SelectionStart) == "number" and not (box.SelectionStart > #string.split(ok,"")) then
            local oof2 = string.split(ok,"")
            local hmm = ""
            local start_pos = box.SelectionStart
            local end_pos = box.CursorPosition
            if end_pos > #oof2 then end_pos = end_pos - 1 end
            if end_pos < start_pos then
                local cup = end_pos
                end_pos = start_pos
                start_pos = cup
                cup = nil
            end
            oof2[start_pos] = "<u>"..oof2[start_pos]
            oof2[end_pos] = oof2[end_pos].."</u>"
            for i,v in pairs(oof2) do
                hmm = hmm..v
            end
            label.Text = hmm
            return
        end
        local stuff = {}
        for i,v in lexer.scan(ok) do
            table.insert(stuff,{i,v})
        end
        for a,b in pairs(stuff) do
            local i = b[1]
            local v = b[2]
            if not v or v == "" then continue end
            if colors[i] then
                if bold[i] or (stuff[a+1] and stuff[a+1][1] == "(") then
                    label.Text = label.Text.."<b>"..'<font color= "'..colors[i]..'">'..v..'</font>'.."</b>"
                else
                    label.Text = label.Text..'<font color= "'..colors[i]..'">'..v..'</font>'
                end
            else
                if bold[i] or (stuff[a+1] and stuff[a+1][1] == "(") then
                    label.Text = label.Text.."<b>"..'<font color= "rgb(255, 255, 255)">'..v..'</font>'.."</b>"
                else
                    label.Text = label.Text..'<font color= "rgb(255, 255, 255)">'..v..'</font>'
                end
            end
        end
        if #string.split(box.Text,"\n") > 30 then
            label.Text = '[OUTPUT TOO BIG, PLEASE RIGHT CLICK THE REMOTE AND CLICK "Copy Script"]'
        end
    end
    box:GetPropertyChangedSignal("Text"):Connect(change)
    box.Focused:Connect(change)
    box.FocusLost:Connect(change)
    box:GetPropertyChangedSignal("SelectionStart"):Connect(change)
    box:GetPropertyChangedSignal("CursorPosition"):Connect(change)
    change()
    label.Parent = box.Parent
    return label
end
local call_func = function(func,...)
    setcontext(6)
    return func(...)
end
local old_rc_frame
local RightClick
local ValChanger
local get_srv_a = function(srv)
    if (not srv) or typeof(srv) == "userdata" then return "ERR_NIL_INST" end
    if srv.Parent == nil then
        return "[nil]"
    end
    if srv.Parent == game then
        return srv
    end
    repeat srv = srv.Parent until not srv or srv.Parent == game
    if not srv then return "ERR_NIL_INST" end
    return srv
end
local ServiceToString = function(inst)
    if typeof(inst) ~= "Instance" then return "[nil]" end
    return [[game:GetService("]]..inst.ClassName..[[")]]
end
local goddam_parse = {
    ["<"] = "&lt;",
    [">"] = "&gt;",
    ["\""] = "&quot;",
    ["'"] = "&apos;",
    ["&"] = "&amp;"
}
local GetPath
GetPath = function(Instance,pass)
    if Instance == game then return "game" end
    if Instance == workspace then return "workspace" end
    if not Instance then return "nil" end
    if Instance.Parent == game then return 'game["'..tostring(Instance)..'"]' end
    if Instance == LP and not pass then return "game:GetService(\"Players\").LocalPlayer" end
    if Instance == LP.Character and not pass then return "game:GetService(\"Players\").LocalPlayer.Character" end
    if typeof(Instance) == "Instance" then
        if Instance.Parent ~= nil then
            local stuff = string.split(Instance:GetFullName(),".")
            local passed = false
            local newstuff = {}
            for i,v in pairs(stuff) do
                if passed then
                    table.insert(newstuff,v)
                else
                    passed = true
                end
            end
            local almost = (Instance.Parent ~= game and ServiceToString(get_srv_a(Instance))) or ServiceToString(Instance)
            local thing_name = newstuff[#newstuff]
            local new_thing_name = ""
            for i,v in pairs(string.split(thing_name,"")) do
                if goddam_parse[v] then
                    new_thing_name = new_thing_name..goddam_parse[v]
                elseif v == "<" or v == ">" or v == "\n" or v == "\t" or (not string.match(v,"%a") and not string.match(v,"%p") and not string.match(v,"%s") and not string.match(v,"%d")) then
                    new_thing_name = new_thing_name.."\\"..tostring(string.byte(v))
                else
                    new_thing_name = new_thing_name..v
                end
            end
            newstuff[#newstuff] = new_thing_name
            for i,v in pairs(newstuff) do
                if not string.split(v,"")[1] then
                    almost = almost.."[\"\"]"
                elseif string.find(v," ") or not string.split(v,"")[1]:match("%a") or v:match("%p") then
                    almost = almost.."[\""..v.."\"]"
                else
                    almost = almost.."."..v
                end
            end
            if typeof(LP.Character) == "Instance" and Instance:IsDescendantOf(LP.Character) then
                almost = "game:GetService(\"Players\").LocalPlayer.Character"..string.sub(almost,1+#GetPath(LP.Character,true))
            end
            if typeof(LP) == "Instance" and Instance:IsDescendantOf(LP) then
                almost = "game:GetService(\"Players\").LocalPlayer"..string.sub(almost,1+#GetPath(LP,true))
            end
            return almost
        else
            return "[nil]."..Instance:GetFullName()
        end
    else
        return tostring(Instance)
    end
end
GetType = function(Instance)
    local Types = {
        ["enumitem"] = function()
            return "Enum." .. tostring(Instance.EnumType) .. "." .. tostring(Instance.Name)
        end,
        ["instance"] = function()
            return GetPath(Instance)
        end,
        ["cframe"] = function()
            return "CFrame.new(" .. tostring(Instance) .. ")"
        end,
        ["vector3"] = function()
            return "Vector3.new(" .. tostring(Instance) .. ")"
        end,
        ["brickcolor"] = function()
            return "BrickColor.new(\"" .. tostring(Instance) .. "\")"
        end,
        ["color3"] = function()
            return "Color3.new(" .. tostring(Instance) .. ")"
        end,
        ["string"] = function()
            local new_thing_name = ""
            for i,v in pairs(string.split(Instance,"")) do
                if v == "<" or v == ">" or v == "\n" or v == "\t" or (not string.match(v,"%a") and not string.match(v,"%p") and not string.match(v,"%s") and not string.match(v,"%d")) then
                    new_thing_name = new_thing_name.."\\"..tostring(string.byte(v))
                else
                    new_thing_name = new_thing_name..v
                end
            end
            if string.find(new_thing_name,"\"") or string.find(new_thing_name,"'") or string.find(new_thing_name,"\n") then
                return "[["..tostring(new_thing_name).."]]"
            else
                return [["]]..tostring(new_thing_name)..[["]]
            end
        end,
        ["ray"] = function()
            return "Ray.new(Vector3.new(" .. tostring(Instance.Origin) .. "), Vector3.new(" .. tostring(Instance.Direction) .. "))"
        end,
        ["number"] = function()
            return tostring(Instance)
        end,
        ["userdata"] = function()
            return "newproxy(false)"
        end,
        ["table"] = function()
            if Instance == {} or (#Instance == 0 and next(Instance) == nil) then
                return "{}"
            else
                return "{'CheatBlox: something wierd here?'}"
            end
        end
    }
    if Types[string.lower(typeof(Instance))] ~= nil then
        return Types[string.lower(typeof(Instance))]()
    else
        return tostring(Instance)
    end
end
local TableString
TableString = function(T,N)
    if not N then N = 1 end
    local M = {}
    for i, v in pairs(T) do
        local I = "\n"..string.rep("    ",N).."[".. ValueToString(i,true) .. "] = "
        table.insert(M, tostring(I) .. (type(v) == "table" and N < 100 and TableString(v,N+1) or ValueToString(v,true)))
    end
    local str_to_ret = "{" .. table.concat(M, ", ") .. "\n"..string.rep("    ",N-1).."}"
    if #str_to_ret > 100000 then
        return '{"table too long!"}'
    else    
        return str_to_ret
    end
end

local ButtonLog = game:GetObjects("rbxassetid://6581395233")[1]
CreateButtonLog = function(txt,color,icon,size)
    local new_button = ButtonLog:Clone()
    new_button:WaitForChild("RemoteNameLabel").Text = txt
    new_button:WaitForChild("RemoteNameLabel").TextColor3 = color
    if type(icon) == "string" and icon ~= "" then
        new_button:WaitForChild("RemoteIcon").Image = icon
    else
        new_button:WaitForChild("RemoteIcon"):Destroy()
        new_button:WaitForChild("RemoteNameLabel").Size = UDim2.new(1,0,1,0)
    end
    if size then
        new_button:WaitForChild("RemoteNameLabel").TextSize = size
    end
    for i,v in pairs(new_button:GetDescendants()) do
        v.Name = ran_gen(100)
    end
    new_button.Name = ran_gen(100)
    MakeCoolButton(new_button)
    return new_button
end
local FindRemotes
FindRemotes = function(thing,in_what)
    for i,v in pairs(thing:GetChildren()) do
        if v:IsDescendantOf(game:GetService("RobloxReplicatedStorage")) then continue end
        if v.Name == "CharacterSoundEvent" and v:IsDescendantOf(workspace) then continue end
        if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
            local has_been_logged = false
            for i2,v2 in pairs(in_what) do
                if v2 == v then
                    has_been_logged = true
                    break
                end
            end
            if not has_been_logged then
                table.insert(in_what,v)
            end
        end
        FindRemotes(v,in_what)
    end
end
---// Protection //---

if syn.protect_gui and not Sirhurt and not PROTOSMASHER_LOADED then
    syn.protect_gui(CheatBlox)
end


---// Sounds //---

local BeepSound = CheatBlox:WaitForChild("Beep")
local ClickSound = CheatBlox:WaitForChild("Click")
local RightClickSound = CheatBlox:WaitForChild("RightClick")

---// Main Frames //---

local ChangerFrame = CheatBlox:WaitForChild("ChangerFrame")
local MainFrame = CheatBlox:WaitForChild("MainFrame")
local MenuFrame = MainFrame:WaitForChild("MenuFrame")
local separator = MenuFrame:WaitForChild("MenuSeparatorFrame")
local TopFrame = MainFrame:WaitForChild("TopFrame")
local HomeFrame = MainFrame:WaitForChild("HomeFrame")
local RemoteSpyFrame = MainFrame:WaitForChild("RemoteSpyFrame")
local RemotesFrame = MainFrame:WaitForChild("RemotesFrame")
local DebugFrame = MainFrame:WaitForChild("DebugFrame")
local IgnoredCallsFrame = MainFrame:WaitForChild("IgnoredCallsFrame")
local ScriptListFrame = MainFrame:WaitForChild("ScriptListFrame")
local ScriptSpyFrame = MainFrame:WaitForChild("ScriptSpyFrame")
local ValFinderFrame = MainFrame:WaitForChild("ValFinderFrame")
local SettingsFrame = MainFrame:WaitForChild("SettingsFrame")
local TransitionFrame = MainFrame:WaitForChild("TransitionFrame")

---// Changer Frame Elements //---

local ChangerTopFrame = ChangerFrame:WaitForChild("TopFrame")
local ChangerLastValueBox = ChangerFrame:WaitForChild("LastValueBox")
local ChangerNewValueBox = ChangerFrame:WaitForChild("NewValueBox")
local ChangeValueButton = ChangerFrame:WaitForChild("ChangeValueButton")
local CancelChangeButton = ChangerFrame:WaitForChild("CancelChangeButton")


---// Menu Buttons //---

local MenuButton = TopFrame:WaitForChild("MenuButton")
local HomeButton = MenuFrame:WaitForChild("HomeButton")
local RemoteSpyButton = MenuFrame:WaitForChild("RemoteSpyButton")
getgenv().testbutton = RemoteSpyButton
local RemotesButton = MenuFrame:WaitForChild("RemotesButton")
local IgnoredCallsButton = MenuFrame:WaitForChild("IgnoredCallsButton")
local DebugButton = MenuFrame:WaitForChild("DebugButton")
local ScriptSpyButton = MenuFrame:WaitForChild("ScriptSpyButton")
local ValueFinderButton = MenuFrame:WaitForChild("ValueFinderButton")
local ScriptListButton = MenuFrame:WaitForChild("ScriptListButton")
local SettingsButton = MenuFrame:WaitForChild("SettingsButton")
local ClearButton = MenuFrame:WaitForChild("ClearButton")

local EnabledButton = SettingsFrame:WaitForChild("EnabledButton")
local SoundsButton = SettingsFrame:WaitForChild("SoundsButton")

---// Other Elements //---

local RemoteLogsFrame = RemotesFrame:WaitForChild("RemoteLogsFrame")
local ScriptListLogsFrame = ScriptListFrame:WaitForChild("ScriptListLogsFrame")
local RemoteSpyLogsFrame = RemoteSpyFrame:WaitForChild("RemoteLogsFrame")
local RemoteSpyOuputFrame = RemoteSpyFrame:WaitForChild("RemoteOutputFrame")
local RemoteOutputBox = RemoteSpyOuputFrame:WaitForChild("RemoteOutputBox")
local FireRemoteButton = RemoteSpyFrame:WaitForChild("FireRemoteButton")
local IgnoreRemoteButton = RemoteSpyFrame:WaitForChild("IgnoreRemoteButton")
local IgnoreRemoteCallButton = RemoteSpyFrame:WaitForChild("IgnoreCallButton")
local IgnoredLogsFrame = IgnoredCallsFrame:WaitForChild("IgnoredLogsFrame")
local IgnoredOutputFrame = IgnoredCallsFrame:WaitForChild("IgnoredOutputFrame")
local IgnoredOutputBox = IgnoredOutputFrame:WaitForChild("IgnoredOutputBox")
local EnableCallButton = IgnoredCallsFrame:WaitForChild("EnableCallButton")
local FunctionLogsFrame = DebugFrame:WaitForChild("FunctionLogsFrame")
local UpvalueLogsFrame = DebugFrame:WaitForChild("UpvalueLogsFrame")
local ConstantLogsFrame = DebugFrame:WaitForChild("ConstantLogsFrame")
local ProtoLogsFrame = DebugFrame:WaitForChild("ProtoLogsFrame")
local EnvLogsFrame = DebugFrame:WaitForChild("EnvLogsFrame")
local ScriptSpyLogsFrame = ScriptSpyFrame:WaitForChild("ScriptLogsFrame")
local ScriptSpyOutputFrame = ScriptSpyFrame:WaitForChild("ScriptOutputFrame")
local ScriptSpyOutputBox = ScriptSpyOutputFrame:WaitForChild("ScriptOutputBox")
local SpyButton = ScriptSpyFrame:WaitForChild("SpyButton")
local UnspyButton = ScriptSpyFrame:WaitForChild("UnspyButton")
local SearchValButton = ValFinderFrame:WaitForChild("SearchValButton")
local ValBox = ValFinderFrame:WaitForChild("ValBox")
local ValTypeBox = ValFinderFrame:WaitForChild("ValTypeBox")
local ValLogsFrame = ValFinderFrame:WaitForChild("ValLogsFrame")
local ChangelogLabel = HomeFrame:WaitForChild("ChangelogLabel")
ChangelogLabel.RichText = true
ChangelogLabel.Text = ChangelogText 

local function GetFuncName(func)
    local name = ""
    pcall(function()
        name = debug.getinfo(func).name
    end)
    return name
end
ValueToString = function(val,frmt)
    if val == "\00" then
        return '"\\00"'
    end
    if type(val) == "table" and val == {} then
        return "{}"
    end
    if val == math.huge then
        return "math.huge"
    end
    if val == -math.huge then
        return "-math.huge"
    end
    if val == math.huge/math.huge then
        return "math.huge/math.huge"
    end
    if val == nil then
        return "nil"
    end
    local tostr_val = ""
    if type(val) == "function" then
        if frmt == true then
            return "function() end"
        end
        tostr_val = "function"
        pcall(function()
            tostr_val = GetFuncName(val)
        end)
        if tostr_val == "" or tostr_val == " "or tostr_val == nil then
            tostr_val = "function"
        end
        tostr_val = tostr_val.."(#"..tostring(debug.getinfo(val).numparams)..")"
        if not islclosure(val) then
            tostr_val = tostr_val.." [C]"
        end
    else
        tostr_val = GetType(val)
    end
    return tostr_val
end


local RightClickOptions
RightClickOptions = {
    ["FireRemote"] = {
        [1] = {
            ["Text"] = "Fire 10x",
            ["Function"] = function(rem,args)
                for i = 1,10 do
                    if rem:IsA("RemoteFunction") then
                        rem:InvokeServer(unpack(args))
                    else
                        rem:FireServer(unpack(args))
                    end
                    game:GetService("RunService").RenderStepped:Wait()
                end
            end
        },
        [2] = {
            ["Text"] = "Fire 20x",
            ["Function"] = function(rem,args)
                for i = 1,20 do
                    if rem:IsA("RemoteFunction") then
                        rem:InvokeServer(unpack(args))
                    else
                        rem:FireServer(unpack(args))
                    end
                    game:GetService("RunService").RenderStepped:Wait()
                end
            end
        }
    },
    ["Function"] = {
        [1] = {
            ["Text"] = "Copy",
            ["Function"] = function(func)
                toclipboard(ValueToString(func))
            end
        },
        [2] = {
            ["Text"] = "Decompile and Copy",
            ["Function"] = function(func)
                if is_synapse_function(func) then toclipboard("Can't decompile synapse function!") return end
                toclipboard(decompile(func))
            end
        },
        [3] = {
            ["Text"] = "Send to Debug",
            ["Function"] = function(func)
                LogFunction(func)
            end
        },
        [4] = {
            ["Text"] = "To Script",
            ["Function"] = function(func)
                local str = "local function ReturnFunc()\n    for i,v in pairs(getgc()) do\n        if type(v) == \"function\" and islclosure(v) then\n"
                str = str.."            if #debug.getconstants(v) == "..tostring(#debug.getconstants(func)) -- add more conds
                local capped_stuff = 0
                for i,v in pairs(debug.getconstants(func)) do
                    if capped_stuff >= 10 then break end
                    if type(v) == "string" then
                        str = str.." and debug.getconstants(v)["..tostring(i).."] == [["..tostring(v).."]]"
                        capped_stuff = capped_stuff + 1
                    elseif type(v) == "number" then
                        str = str.." and debug.getconstants(v)["..tostring(i).."] == "..tostring(v)
                        capped_stuff = capped_stuff + 1
                    end
                end
                str = str.." then\n                return v\n            end\n"
                str = str.."        end\n    end\nend"
                toclipboard(str)
            end
        },
        [5] = {
            ["Text"] = "Get Similar Functions",
            ["Function"] = function(func)
                local found_funcs = {}
                for i,v in pairs(getgc()) do
                    if type(v) == "function" and v ~= func and not is_synapse_function(v) and islclosure(v) then
                        local dup = false
                        for i2,v2 in pairs(found_funcs) do
                            if v == v2 then
                                dup = true
                                break
                            end
                        end
                        if not dup and (tostring(getfenv(v)["script"]) == tostring(getfenv(func)["script"])) then
                            table.insert(found_funcs,v)
                        end
                        for i2,v2 in pairs(debug.getupvalues(v)) do
                            if type(v2) == "function" and not is_synapse_function(v2) and islclosure(v2) and tostring(v2) ~= tostring(func) then
                                if not (tostring(getfenv(v2)["script"]) ~= tostring(getfenv(func)["script"])) then
                                    table.insert(found_funcs,v2)
                                end
                            elseif type(v2) == "table" then
                                local rec
                                rec = function(tbl,N)
                                    if type(N) == "number" and N > 5 then return end
                                    if not N then N = 0 end
                                    for i3,v3 in pairs(tbl) do
                                        if type(v3) == "function" and v3 ~= func and not is_synapse_function(v3) and islclosure(v3) then
                                            if not (tostring(getfenv(v3)["script"]) ~= tostring(getfenv(func)["script"])) then
                                                table.insert(found_funcs,v3)
                                            end
                                        elseif type(v) == "table" then
                                            rec(v,N+1)
                                        end
                                    end
                                end
                                rec(v2,0)
                            end
                        end
                        for i2,v2 in pairs(debug.getconstants(v)) do
                            if type(v2) == "function" and not is_synapse_function(v2) and islclosure(v2) and tostring(v2) ~= tostring(func) then
                                if not (tostring(getfenv(v2)["script"]) ~= tostring(getfenv(func)["script"])) then
                                    table.insert(found_funcs,v2)
                                end
                            elseif type(v2) == "table" then
                                local rec
                                rec = function(tbl,N)
                                    if type(N) == "number" and N > 5 then return end
                                    if not N then N = 0 end
                                    for i3,v3 in pairs(tbl) do
                                        if type(v3) == "function" and v3 ~= func and not is_synapse_function(v3) and islclosure(v3) then
                                            if not (tostring(getfenv(v3)["script"]) ~= tostring(getfenv(func)["script"])) then
                                                table.insert(found_funcs,v3)
                                            end
                                        elseif type(v) == "table" then
                                            rec(v,N+1)
                                        end
                                    end
                                end
                                rec(v2,0)
                            end
                        end
                    end
                end
                for i,v in pairs(found_funcs) do
                    LogFunction(v)
                end
            end
        }
    },
    ["DebugVal"] = {
        [1] = {
            ["Text"] = "Copy",
            ["Function"] = function(val,func)
                if type(val) == "string" then
                    toclipboard([["]]..tostring(val)..[["]])
                elseif type(val) == "table" then
                    toclipboard(TableString(val))
                elseif typeof(val) == "Instance" then
                    toclipboard(val:GetFullName())
                else
                    toclipboard(tostring(val))
                end
            end
        },
        [2] = {
            ["Text"] = "Debug Func",
            ["Function"] = function(val,func)
                LogFunction(func)
            end
        }
    },
    ["DebugFunc"] = {
        [1] = {
            ["Text"] = "Copy",
            ["Function"] = function(val,func)
                if type(val) == "string" then
                    toclipboard([["]]..tostring(val)..[["]])
                elseif type(val) == "table" then
                    toclipboard(TableString(val))
                elseif typeof(val) == "Instance" then
                    toclipboard(val:GetFullName())
                else
                    toclipboard(tostring(val))
                end
            end
        },
        [2] = {
            ["Text"] = "Debug Func",
            ["Function"] = function(val,func)
                LogFunction(func)
            end
        },
        [3] = {
            ["Text"] = "Send to Debug",
            ["Function"] = function(val,func)
                LogFunction(val)
            end
        }
    },
    ["RemoteDebug"] = {
        [1] = {
            ["Text"] = "Copy",
            ["Function"] = function(rem,func,toscr)
                setclipboard(rem:GetFullName())
            end
        },
        [2] = {
            ["Text"] = "Copy Script",
            ["Function"] = function(rem,func,toscr)
                setclipboard(toscr)
            end
        },
        [3] = {
            ["Text"] = "Send to Debug",
            ["Function"] = function(rem,func,toscr)
                LogFunction(func)
            end
        }
    },
    ["Script"] = {
        [1] = {
            ["Text"] = "Copy",
            ["Function"] = function(scr)
                setclipboard(ValueToString(scr))
            end
        },
        [2] = {
            ["Text"] = "Decompile and Copy",
            ["Function"] = function(scr)
                setclipboard(decompile(scr))
            end
        },
        [3] = {
            ["Text"] = "Send to Debug",
            ["Function"] = function(scr)
                LogFunction(getscriptfunction(scr))
            end
        },
        [4] = {
            ["Text"] = "Spy",
            ["Function"] = function(scr)
                for i,v in pairs(SpiedScripts) do
                    if v == scr then
                        table.remove(SpiedScripts,i)
                    end
                end
                table.insert(SpiedScripts,scr)
                UpdateScripts()
            end
        },
        [5] = {
            ["Text"] = "Unspy",
            ["Function"] = function(scr)
                for i,v in pairs(SpiedScripts) do
                    if v == scr then
                        table.remove(SpiedScripts,i)
                    end
                end
                UpdateScripts()
            end
        }
    },
    ["Prop"] = {
        [1] = {
            ["Text"] = "Copy Path",
            ["Function"] = function(thing)
                toclipboard(thing:GetFullName())
            end
        }
    }
}














local GetRightClickOptions = function(wat)
    return RightClickOptions[wat]
end

local NewRightClickFrame = function(stuff,args)
    local rc_frame = Instance.new("Frame")
    rc_frame.ZIndex = 999999999
    rc_frame.BorderColor3 = Color3.new(0,0,0)
    rc_frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
    rc_frame.Position = UDim2.new(0,LP:GetMouse().X,0,LP:GetMouse().Y + 36)
    for i,v in pairs(stuff) do
        local choise_button = CreateButtonLog(v.Text,Color3.new(1,1,1),"",16)
        choise_button.Position = UDim2.new(0,0,0,#rc_frame:GetChildren()*20)
        choise_button.MouseButton1Click:Connect(function()
            spawn(function()
                v.Function(unpack(args))
            end)
            rc_frame:Remove()
            spawn(function()
                wait(0.5)
                rc_frame:Destroy()
            end)
        end)
        choise_button.Parent = rc_frame
    end
    rc_frame.Size = UDim2.new(0,150,0,#rc_frame:GetChildren()*20)
    rc_frame.Name = ran_gen(100)
    rc_frame.Parent = CheatBlox
    return rc_frame
end

local RightClick = function(button,wat,args)
    RightClickSound:Play()
    if old_rc_frame then
        old_rc_frame:Destroy()
    end
    old_rc_frame = NewRightClickFrame(GetRightClickOptions(wat),args)
end

local PromptValueChange = function(func,what,index,val)
    ChangerLastValueBox.Text = ValueToString(val)
    ChangerNewValueBox.Text = ""
    local click_con1,click_con2
    click_con1 = ChangeValueButton.MouseButton1Click:Connect(function()
        if click_con1 then
            click_con1:Disconnect()
        end
        if click_con2 then
            click_con2:Disconnect()
        end
        local result_func = loadstring("return "..ChangerNewValueBox.Text)
        if result_func then
            if what == "Upvalue" then
                debug.setupvalue(func,index,result_func())
            elseif what == "Constant" then
                debug.setconstant(func,index,result_func())
            elseif what == "Proto" then
                debug.setproto(func,index,result_func())
            elseif what == "Global" then
                local new_env = getfenv(func)
                new_env[index] = result_func()
                setfenv(func,new_env)
            elseif what == "Prop" then
                func[index] = result_func()
            elseif what == "Table" then
                func[index] = result_func()
            end
        end
        ChangerFrame.Visible = false
        ChangerLastValueBox.Text = ""
        ChangerNewValueBox.Text = ""
    end)
    click_con2 = CancelChangeButton.MouseButton1Click:Connect(function()
        if click_con1 then
            click_con1:Disconnect()
        end
        if click_con2 then
            click_con2:Disconnect()
        end
        ChangerFrame.Visible = false
        ChangerLastValueBox.Text = ""
        ChangerNewValueBox.Text = ""
    end)
    ChangerFrame.Visible = true
    return true
end

LogFunction = function(func)
    local func_button = CreateButtonLog(ValueToString(func),Color3.new(0,1,1),Icons["Function"])
    func_button.Position = UDim2.new(0,0,0,#FunctionLogsFrame:GetChildren()*20)
    func_button.MouseButton1Click:Connect(function()
        for i,v in pairs(UpvalueLogsFrame:GetChildren()) do
            v:Destroy()
        end
        for i,v in pairs(ConstantLogsFrame:GetChildren()) do
            v:Destroy()
        end
        for i,v in pairs(ProtoLogsFrame:GetChildren()) do
            v:Destroy()
        end
        for i,v in pairs(EnvLogsFrame:GetChildren()) do
            v:Destroy()
        end
        for i,v in pairs(debug.getupvalues(func)) do
            local val_button = CreateButtonLog(ValueToString(v),Color3.new(0,1,1),"")
            val_button.Position = UDim2.new(0,0,0,#UpvalueLogsFrame:GetChildren()*20)
            val_button.MouseButton1Click:Connect(function()
                PromptValueChange(func,"Upvalue",i,v)
            end)
            val_button.MouseButton2Click:Connect(function()
                if type(v) == "function" then
                    RightClick(val_button,"DebugFunc",{v,func})
                else
                    RightClick(val_button,"DebugVal",{v,func})
                end
            end)
            val_button.Parent = UpvalueLogsFrame
        end
        for i,v in pairs(debug.getconstants(func)) do
            local val_button = CreateButtonLog(ValueToString(v),Color3.new(0,1,1),"")
            val_button.Position = UDim2.new(0,0,0,#ConstantLogsFrame:GetChildren()*20)
            val_button.MouseButton1Click:Connect(function()
                PromptValueChange(func,"Constant",i,v)
            end)
            val_button.MouseButton2Click:Connect(function()
                if type(v) == "function" then
                    RightClick(val_button,"DebugFunc",{v,func})
                else
                    RightClick(val_button,"DebugVal",{v,func})
                end
            end)
            val_button.Parent = ConstantLogsFrame
        end
        for i,v in pairs(debug.getprotos(func)) do
            local val_button = CreateButtonLog(ValueToString(v),Color3.new(0,1,1),"")
            val_button.Position = UDim2.new(0,0,0,#ProtoLogsFrame:GetChildren()*20)
            val_button.MouseButton1Click:Connect(function()
                PromptValueChange(func,"Proto",i,v)
            end)
            val_button.MouseButton2Click:Connect(function()
                RightClick(val_button,"DebugFunc",{debug.getproto(func,i,true)[1],func})
            end)
            val_button.Parent = ProtoLogsFrame
        end
        for i,v in pairs(getfenv(func)) do
            local val_button = CreateButtonLog(ValueToString(v),Color3.new(0,1,1),"")
            val_button.Position = UDim2.new(0,0,0,#EnvLogsFrame:GetChildren()*20)
            val_button.MouseButton1Click:Connect(function()
                PromptValueChange(func,"Global",i,v)
            end)
            val_button.MouseButton2Click:Connect(function()
                if type(v) == "function" then
                    RightClick(val_button,"DebugFunc",{v,func})
                else
                    RightClick(val_button,"DebugVal",{v,func})
                end
            end)
            val_button.Parent = EnvLogsFrame
        end
        UpvalueLogsFrame.CanvasSize = UDim2.new(0,0,0,#UpvalueLogsFrame:GetChildren()*20)
        ConstantLogsFrame.CanvasSize = UDim2.new(0,0,0,#ConstantLogsFrame:GetChildren()*20)
        ProtoLogsFrame.CanvasSize = UDim2.new(0,0,0,#ProtoLogsFrame:GetChildren()*20)
        EnvLogsFrame.CanvasSize = UDim2.new(0,0,0,#EnvLogsFrame:GetChildren()*20)
    end)
    func_button.MouseButton2Click:Connect(function()
        RightClick(func_button,"Function",{func})
    end)
    func_button.Parent = FunctionLogsFrame
    FunctionLogsFrame.CanvasSize = UDim2.new(0,0,0,#FunctionLogsFrame:GetChildren()*20)
end


local ChangeFrame = function(frm)
    if InTransition then return false end
    InTransition = true
    TransitionFrame.Visible = true
    MenuOpened = false
    MenuButton.Text = ">"
    separator.Visible = false
    TweenService:Create(MenuFrame,FastTween,{["AnchorPoint"] = Vector2.new(1,0)}):Play()
    TweenService:Create(TransitionFrame,FastTween,{["Size"] = UDim2.new(1,0,1,-20)}):Play()
    wait(0.31)
    HomeFrame.Visible = false
    RemoteSpyFrame.Visible = false
    RemotesFrame.Visible = false
    DebugFrame.Visible = false
    ValFinderFrame.Visible = false
    IgnoredCallsFrame.Visible = false
    ScriptListFrame.Visible = false
    ScriptSpyFrame.Visible = false
    SettingsFrame.Visible = false
    frm.Visible = true
    TweenService:Create(TransitionFrame,FastTween,{["Size"] = UDim2.new(0,0,1,-20)}):Play()
    wait(0.31)
    TransitionFrame.Visible = false
    InTransition = false
    return true
end


UpdateRemotes = function()
    local FoundRemotes = {}
    FindRemotes(game,FoundRemotes)
    for i,v in pairs(RemoteLogsFrame:GetChildren()) do
        v:Destroy()
    end
    for i,v in pairs(FoundRemotes) do
        local rem_button
        local IsRemIgnored = false
        for i2,v2 in pairs(IgnoredRemotes) do
            if v2 == v then
                IsRemIgnored = true
                break
            end
        end
        if IsRemIgnored then
            if v:IsA("RemoteEvent") then
                rem_button = CreateButtonLog(ValueToString(v),Color3.new(0.6,0.6,0.6),Icons["RemoteEvent"])
            else
                rem_button = CreateButtonLog(ValueToString(v),Color3.new(0.6,0.6,0.6),Icons["RemoteFunction"])
            end
        else
            if v:IsA("RemoteEvent") then
                rem_button = CreateButtonLog(ValueToString(v),Color3.new(1,1,0),Icons["RemoteEvent"])
            else
                rem_button = CreateButtonLog(ValueToString(v),Color3.new(1,0,1),Icons["RemoteFunction"])
            end
        end
        rem_button.Position = UDim2.new(0,0,0,#RemoteLogsFrame:GetChildren()*20)
        rem_button.Size = UDim2.new(1,0,0,20)
        rem_button.MouseButton1Click:Connect(function()
            for i2,v2 in pairs(IgnoredRemotes) do
                if v2 == v then
                    table.remove(IgnoredRemotes,i2)
                    UpdateRemotes()
                    return
                end
            end
            table.insert(IgnoredRemotes,v)
            UpdateRemotes()
        end)
        rem_button.Parent = RemoteLogsFrame
    end
    RemoteLogsFrame.CanvasSize = UDim2.new(0,0,0,#RemoteLogsFrame:GetChildren()*20)
end

UpdateScripts = function()
    local FoundScripts = {}
    for i,v in pairs(getscripts()) do
        if v and v:IsA("LocalScript") then
            if v.Name == "Animate" or v:IsDescendantOf(game:GetService("Chat")) or v:IsDescendantOf(game:GetService("CoreGui")) then continue end
            table.insert(FoundScripts,v)
        end
    end
    for i,v in pairs(getscripts()) do
        if v:IsA("ModuleScript") then
            if v.Name == "Animate" or v:IsDescendantOf(game:GetService("Chat")) or v:IsDescendantOf(game:GetService("CoreGui")) then continue end
            table.insert(FoundScripts,v)
        end
    end
    for i,v in pairs(ScriptListLogsFrame:GetChildren()) do
        v:Destroy()
    end
    for i,v in pairs(FoundScripts) do
        local scr_button
        local is_it_spied = false
        for i2,v2 in pairs(SpiedScripts) do
            if v2 == v then
                is_it_spied = true
                break
            end
        end
        if is_it_spied then
            if v:IsA("ModuleScript") then
                scr_button = CreateButtonLog(ValueToString(v),Color3.new(0.9,1,1),Icons["ModuleScript"])
            else
                scr_button = CreateButtonLog(ValueToString(v),Color3.new(0.9,1,1),Icons["LocalScript"])
            end
        else
            if v:IsA("ModuleScript") then
                scr_button = CreateButtonLog(ValueToString(v),Color3.new(0,1,1),Icons["ModuleScript"])
            else
                scr_button = CreateButtonLog(ValueToString(v),Color3.new(0,1,1),Icons["LocalScript"])
            end
        end
        scr_button.Position = UDim2.new(0,0,0,#ScriptListLogsFrame:GetChildren()*20)
        scr_button.Size = UDim2.new(1,0,0,20)
        scr_button.MouseButton2Click:Connect(function()
            RightClick(scr_button,"Script",{v})
        end)
        scr_button.Parent = ScriptListLogsFrame
    end
    ScriptListLogsFrame.CanvasSize = UDim2.new(0,0,0,#ScriptListLogsFrame:GetChildren()*20)
end
local ToScript = function(object,scr,fnc,method,hidden, ...)
    local script = "--// Script: "..GetPath(scr)
    local f_name = ValueToString(fnc)
    script = script.."\n--// Function: "..f_name.."\n"
    if hidden then
        script = script.."--// Hidden (Index Firing)\n"
    end
    script = script.."\n"
    local args = {}
    local stuff_idk = {...}
    for i = 1,#stuff_idk do
        v = stuff_idk[i]
        script = script.."local v"..tostring(i).." = "
        if type(v) == "table" then
            if getrawmetatable(v) then
                local new_secure_table = {}
                for i2,v2 in pairs(v) do
                    new_secure_table[i2] = v2
                end
                script = script..TableString(new_secure_table)
            else
                script = script..TableString(v)
            end
        elseif type(v) == "function" then
            script = script.."function() end"
        else
            script = script..ValueToString(v,true)
        end
        script = script.."\n"
        table.insert(args, "v" .. i)
    end
    if string.find(string.lower(object.ClassName),"remote") or string.find(string.lower(object.ClassName),"bindable") then
        script = script .. "local event = " .. GetPath(object) .. "\n\n"
    else
        script = script .. "local thing = " .. GetPath(object) .. "\n\n"
    end
    if string.find(string.lower(object.ClassName),"remote") or string.find(string.lower(object.ClassName),"bindable") then
        script = script .. "event:" .. method .. "(" .. table.concat(args, ", ") .. ")"
    else
        script = script .. "thing:" .. method .. "(" .. table.concat(args, ", ") .. ")"
    end
    return script
end
local OnRemote = function(MAIN_INFO)
    setcontext(6)
    local rem_button
    if MAIN_INFO["rem"]:IsA("RemoteEvent") then
        rem_button = CreateButtonLog(MAIN_INFO["rem"].Name,Color3.new(1,1,0),Icons["RemoteEvent"],12)
    elseif MAIN_INFO["rem"]:IsA("RemoteFunction") then
        rem_button = CreateButtonLog(MAIN_INFO["rem"].Name,Color3.new(1,0,1),Icons["RemoteFunction"],12)
    end
    if Settings["Sounds"] then
        BeepSound:Play()
    end
    rem_button.MouseButton1Click:Connect(function()
        SelectedRemote = MAIN_INFO["rem"]
        SelectedRemoteArgs = MAIN_INFO["args"]
        RemoteOutputBox.Text = MAIN_INFO["ToScript"]
    end)
    rem_button.MouseButton2Click:Connect(function()
        RightClick(rem_button,"RemoteDebug",{MAIN_INFO["rem"],MAIN_INFO["Function"],MAIN_INFO["ToScript"]})
    end)
    rem_button.Position = UDim2.new(0,0,0,#RemoteSpyLogsFrame:GetChildren()*20)
    rem_button.Parent = RemoteSpyLogsFrame
    RemoteSpyLogsFrame.CanvasSize = UDim2.new(0,0,0,#RemoteSpyLogsFrame:GetChildren()*20)
    return true
end
local OnScript = function(MAIN_INFO)
    setcontext(6)
    local scr_button
    if MAIN_INFO["Type"] == 1 then
        scr_button = CreateButtonLog("__index",Color3.new(0,1,1),Icons["Value"],12)
    elseif MAIN_INFO["Type"] == 2 then
        scr_button = CreateButtonLog("__newindex",Color3.new(0,1,1),Icons["Value"],12)
    else
        scr_button = CreateButtonLog("__namecall",Color3.new(0,1,1),Icons["Value"],12)
    end
    scr_button.MouseButton1Click:Connect(function()
        if MAIN_INFO["Type"] == 1 then
            ScriptSpyOutputBox.Text = "--// Script: "..ValueToString(MAIN_INFO["scr"]).."\nSelf: "..ValueToString(MAIN_INFO["Self"]).."\nIndex: "..tostring(MAIN_INFO["Index"]).."\nReturn: "..ValueToString(MAIN_INFO["Return"])
        elseif MAIN_INFO["Type"] == 2 then
            ScriptSpyOutputBox.Text = "--// Script: "..ValueToString(MAIN_INFO["scr"]).."\nSelf: "..ValueToString(MAIN_INFO["Self"]).."\nIndex: "..tostring(MAIN_INFO["Index"]).."\nValue: "..ValueToString(MAIN_INFO["Val"])
        else
            ScriptSpyOutputBox.Text = ToScript(MAIN_INFO["Self"],MAIN_INFO["scr"],MAIN_INFO["Function"],MAIN_INFO["Method"],false,unpack(MAIN_INFO["Args"]))
        end
    end)
    scr_button.MouseButton2Click:Connect(function()
        RightClick(scr_button,"Function",{MAIN_INFO["Function"]})
    end)
    scr_button.Position = UDim2.new(0,0,0,#ScriptSpyLogsFrame:GetChildren()*20)
    scr_button.Parent = ScriptSpyLogsFrame
    ScriptSpyLogsFrame.CanvasSize = UDim2.new(0,0,0,#ScriptSpyLogsFrame:GetChildren()*20)
    return true
end
local remote_bindable = Instance.new("BindableEvent")
remote_bindable.Event:Connect(function(what,...)
    if what == "OnRemote" then
        OnRemote(...)
    end
end)
local FireServerBackup
local InvokeServerBackup
local FireServerHook = newcclosure(function(self,...)
    if not Settings["Enabled"] then return FireServerBackup(self,...) end
    local args = {...}
    for i,v in pairs(IgnoredRemotes) do
        if self == v then
            return FireServerBackup(self,...)
        end
    end
    for a,b in pairs(IgnoredCalls) do
        if b.Remote ~= self then continue end
        for i,v in pairs(args) do
            for i2,v2 in pairs(b["Args"]) do
                if type(v2) == "string" and type(v) == "string" and v == v2 then
                    return FireServerBackup(self,...)
                end
            end
        end
    end
    local scr = getcallingscript() or getfenv(3)["script"] or "Unknown"
    local RemoteStuff = {
        ["scr"] = scr,
        ["rem"] = self,
        ["args"] = args,
        ["method"] = "FireServer",
        ["Hidden"] = true,
        ["Function"] = getcallingfunction(3),
        ["ToScript"] = ToScript(self,scr,getcallingfunction(3),"FireServer",true,unpack(args))
    }
    remote_bindable.Fire(remote_bindable,"OnRemote",RemoteStuff)
    return FireServerBackup(self,...)
end)
local InvokeServerHook = newcclosure(function(self,...)
    if not Settings["Enabled"] then return InvokeServerBackup(self,...) end
    local args = {...}
    for i,v in pairs(IgnoredRemotes) do
        if self == v then
            return InvokeServerBackup(self,...)
        end
    end

    for a,b in pairs(IgnoredCalls) do
        if b.Remote ~= self then continue end
        for i,v in pairs(args) do
            for i2,v2 in pairs(b["Args"]) do
                if type(v2) == "string" and type(v) == "string" and v == v2 then
                    return InvokeServerBackup(self,...)
                end
            end
        end
    end
    local scr = getcallingscript() or getfenv(3)["script"] or "Unknown"
    local RemoteStuff = {
        ["scr"] = scr,
        ["rem"] = self,
        ["args"] = args,
        ["method"] = "InvokeServer",
        ["Hidden"] = true,
        ["Function"] = getcallingfunction(3),
        ["ToScript"] = ToScript(self,scr,getcallingfunction(3),"InvokeServer",true,unpack(args))
    }
    remote_bindable.Fire(remote_bindable,"OnRemote",RemoteStuff)
    return InvokeServerBackup(self,...)
end)
FireServerBackup = hookfunction(Instance.new("RemoteEvent").FireServer,FireServerHook)
InvokeServerBackup = hookfunction(Instance.new("RemoteFunction").InvokeServer,InvokeServerHook)
backup = hookmetamethod(game,"__namecall",function(self,...)
    if not Settings["Enabled"] then return backup(self,...) end
    if getcontext() == 6 or checkcaller() or getnamecallmethod() == "Fire" then return backup(self,...) end
    local OLD_NC = getnamecallmethod()
    local args = {...}
    for i,v in pairs(IgnoredRemotes) do
        if self == v then
            return backup(self,...)
        end
    end
    local spy_it = false
    for i,v in pairs(SpiedScripts) do
        if v == getfenv(3)["script"] then
            spy_it = true
            break
        end
    end
    if getnamecallmethod() == "FireServer" and self.ClassName == "RemoteEvent" then
        for a,b in pairs(IgnoredCalls) do
            if b.Remote ~= self then continue end
            for i,v in pairs(args) do
                for i2,v2 in pairs(b["Args"]) do
                    if type(v2) == "string" and type(v) == "string" and v == v2 then
                        return backup(self,...)
                    end
                end
            end
        end
        local scr = getcallingscript() or getfenv(3)["script"] or "Unknown"
        local RemoteStuff = {
            ["scr"] = scr,
            ["rem"] = self,
            ["args"] = args,
            ["method"] = "FireServer",
            ["Function"] = getcallingfunction(3),
            ["ToScript"] = ToScript(self,scr,getcallingfunction(3),"FireServer",false,unpack(args))
        }
        remote_bindable:Fire("OnRemote",RemoteStuff)
    end
    if getnamecallmethod() == "InvokeServer" and self.ClassName == "RemoteFunction" then
        for a,b in pairs(IgnoredCalls) do
            if b.Remote ~= self then continue end
            for i,v in pairs(args) do
                for i2,v2 in pairs(b["Args"]) do
                    if type(v2) == "string" and type(v) == "string" and v == v2 then
                        return backup(self,...)
                    end
                end
            end
        end
        local scr = getcallingscript() or getfenv(3)["script"] or "Unknown"
        local RemoteStuff = {
            ["scr"] = scr,
            ["rem"] = self,
            ["args"] = args,
            ["method"] = "InvokeServer",
            ["Function"] = getcallingfunction(3),
            ["ToScript"] = ToScript(self,scr,getcallingfunction(3),"InvokeServer",false,unpack(args))
        }
        remote_bindable.Fire(remote_bindable,"OnRemote",RemoteStuff)
    end
    setnamecallmethod(OLD_NC)
    if not spy_it then
        return backup(self,...)
    end
    local RET_VAL = backup(self,...)
    local scr = getcallingscript() or getfenv(3)["script"]
    local Stuff = {
        ["Function"] = getcallingfunction(3),
        ["scr"] = scr,
        ["Self"] = self,
        ["Method"] = getnamecallmethod(),
        ["Args"] = args,
        ["Return"] = RET_VAL,
        ["Type"] = 3
    }
    coroutine.resume(coroutine.create(OnScript),Stuff)
    return backup(self,...)
end)
backup2 = hookmetamethod(game,"__index",function(tbl,idx)
    if not SRS_ENABLED then return backup2(tbl,idx) end
    if checkcaller() or getcontext() == 6 then return backup2(tbl,idx) end
    if idx == "CFrame" or idx == "Position" then return backup2(tbl,idx) end
    local spy_it = false
    for i,v in pairs(SpiedScripts) do
        if v == getfenv(3)["script"] then
            spy_it = true
            break
        end
    end
    for i,v in pairs(IgnoredCalls) do
        if tostring(v["Args"][1]) == tostring(tbl) and tostring(v["Args"][2]) == tostring(idx) then
            return backup2(tbl,idx)
        end
    end
    if not spy_it then
        return backup2(tbl,idx)
    end
    local scr = getfenv(3)["script"]
    local Stuff = {
        ["Function"] = getcallingfunction(3),
        ["scr"] = scr,
        ["Self"] = tbl,
        ["Index"] = idx,
        ["Return"] = backup2(tbl,idx),
        ["Type"] = 1
    }
    coroutine.resume(coroutine.create(OnScript),Stuff)
    return backup2(tbl,idx)
end)
backup3 = hookmetamethod(game,"__newindex",function(tbl,idx,val)
    if not SRS_ENABLED then return backup3(tbl,idx,val) end
    if checkcaller() or getcontext() == 6 then return backup3(tbl,idx,val) end
    if idx == "CFrame" or idx == "Position" then return backup3(tbl,idx,val) end
    local spy_it = false
    for i,v in pairs(SpiedScripts) do
        if v == getfenv(3)["script"] then
            spy_it = true
            break
        end
    end
    for i,v in pairs(IgnoredCalls) do
        if tostring(v["Args"][1]) == tostring(tbl) and tostring(v["Args"][2]) == tostring(idx) then
            return backup3(tbl,idx,val)
        end
    end
    if not spy_it then
        return backup3(tbl,idx,val)
    end
    local scr = getfenv(3)["script"]
    local Stuff = {
        ["Function"] = getcallingfunction(3),
        ["scr"] = scr,
        ["Self"] = tbl,
        ["Index"] = idx,
        ["Val"] = val,
        ["Type"] = 2
    }
    coroutine.resume(coroutine.create(OnScript),Stuff)
    return backup3(tbl,idx,val)
end)

local UpdateIgnoredCalls = function()
    for i,v in pairs(IgnoredLogsFrame:GetChildren()) do
        v:Destroy()
    end
    for i,v in pairs(IgnoredCalls) do
        local call_button = CreateButtonLog(v["Remote"].Name,Color3.fromRGB(255,85,0),Icons["Call"],12)
        call_button.MouseButton1Click:Connect(function()
            SelectedCall = i
            IgnoredOutputBox.Text = v["Text"]
        end)
        call_button.Position = UDim2.new(0,0,0,#IgnoredLogsFrame:GetChildren()*20)
        call_button.Parent = IgnoredLogsFrame
        IgnoredLogsFrame.CanvasSize = UDim2.new(0,0,0,#IgnoredLogsFrame:GetChildren()*20)
    end
end

EnableCallButton.MouseButton1Click:Connect(function()
    if not SelectedCall then return end
    table.remove(IgnoredCalls,SelectedCall)
    IgnoredOutputBox.Text = ""
    UpdateIgnoredCalls()
end)

FireRemoteButton.MouseButton1Click:Connect(function()
    if not SelectedRemote or not SelectedRemoteArgs then return end
    if SelectedRemote:IsA("RemoteEvent") then
        SelectedRemote:FireServer(unpack(SelectedRemoteArgs))
    elseif SelectedRemote:IsA("RemoteFunction") then
        SelectedRemote:InvokeServer(unpack(SelectedRemoteArgs))
    end
end)

FireRemoteButton.MouseButton2Click:Connect(function()
    RightClick(FireRemoteButton,"FireRemote",{SelectedRemote,SelectedRemoteArgs})
end)

IgnoreRemoteButton.MouseButton1Click:Connect(function()
    if not SelectedRemote then return end
    table.insert(IgnoredRemotes,SelectedRemote)
end)

IgnoreRemoteCallButton.MouseButton1Click:Connect(function()
    if not SelectedRemote or not SelectedRemoteArgs then return end
    table.insert(IgnoredCalls,{["Remote"] = SelectedRemote,["Args"] = SelectedRemoteArgs,["Text"] = TableString(SelectedRemoteArgs)})
end)

MenuButton.MouseButton1Click:Connect(function()
    if MenuOpened then
        separator.Visible = false
        MenuButton.Text = ">"
        TweenService:Create(MenuFrame,FastTween,{["AnchorPoint"] = Vector2.new(1,0)}):Play()
    else
        separator.Visible = true
        MenuButton.Text = "<"
        TweenService:Create(MenuFrame,FastTween,{["AnchorPoint"] = Vector2.new(0,0)}):Play()
    end
    MenuOpened = not MenuOpened
end)

HomeButton.MouseButton1Click:Connect(function()
    ChangeFrame(HomeFrame)
end)

RemoteSpyButton.MouseButton1Click:Connect(function()
    ChangeFrame(RemoteSpyFrame)
end)

RemotesButton.MouseButton1Click:Connect(function()
    UpdateRemotes()
    ChangeFrame(RemotesFrame)
end)

IgnoredCallsButton.MouseButton1Click:Connect(function()
    UpdateIgnoredCalls()
    ChangeFrame(IgnoredCallsFrame)
end)

DebugButton.MouseButton1Click:Connect(function()
    ChangeFrame(DebugFrame)
end)

ScriptSpyButton.MouseButton1Click:Connect(function()
    ChangeFrame(ScriptSpyFrame)
end)

ValueFinderButton.MouseButton1Click:Connect(function()
    ChangeFrame(ValFinderFrame)
end)

ScriptListButton.MouseButton1Click:Connect(function()
    UpdateScripts()
    ChangeFrame(ScriptListFrame)
end)

SettingsButton.MouseButton1Click:Connect(function()
    ChangeFrame(SettingsFrame)
end)

ClearButton.MouseButton1Click:Connect(function()
    ToClearBox = nil
    ToClearFrame = nil
    if RemoteSpyFrame.Visible then
        ToClearBox = RemoteOutputBox
        ToClearFrame = {RemoteSpyLogsFrame}
        SelectedRemote = nil
        SelectedRemoteArgs = nil
    elseif DebugFrame.Visible then
        ToClearBox = nil
        ToClearFrame = {FunctionLogsFrame,UpvalueLogsFrame,ConstantLogsFrame,ProtoLogsFrame,EnvLogsFrame}
    elseif ScriptSpyFrame.Visible then
        ToClearBox = ScriptSpyOutputBox
        ToClearFrame = {ScriptSpyLogsFrame}
    end
    if ToClearFrame then
        for i,v in pairs(ToClearFrame) do
            for i2,v2 in pairs(v:GetChildren()) do
                v2:Destroy()
            end
        end
    end
    if ToClearBox then
        ToClearBox.Text = ""
    end
    ToClearBox = nil
    ToClearFrame = nil
end)

SpyButton.MouseButton1Click:Connect(function()
    local scr
    pcall(function()
        scr = loadstring("return "..ScriptSpyOutputBox.Text)()
    end)
    if typeof(scr) == "Instance" and (scr:IsA("LocalScript") or scr:IsA("ModuleScript")) then
        for i,v in pairs(SpiedScripts) do
            if v == scr then
                table.remove(SpiedScripts,i)
            end
        end
        table.insert(SpiedScripts,scr)
    end
    ScriptSpyOutputBox.Text = ""
    UpdateScripts()
end)

UnspyButton.MouseButton1Click:Connect(function()
    local scr
    pcall(function()
        scr = loadstring("return "..ScriptSpyOutputBox.Text)()
    end)
    if typeof(scr) == "Instance" and (scr:IsA("LocalScript") or scr:IsA("ModuleScript")) then
        for i,v in pairs(SpiedScripts) do
            if v == scr then
                table.remove(SpiedScripts,i)
            end
        end
    end
    ScriptSpyOutputBox.Text = ""
    UpdateScripts()
end)


local search_types = {["upvalue"] = true,["constant"] = true,["proto"] = true,["global"] = true,["prop"] = true,["table"] = true}
SearchValButton.MouseButton1Click:Connect(function()
    for i,v in pairs(ValLogsFrame:GetChildren()) do
        v:Destroy()
    end
    local to_search_val = (loadstring("return "..ValBox.Text) and loadstring("return "..ValBox.Text)()) or nil
    local to_search_val_type = ValTypeBox.Text
    if not to_search_val or to_search_val == "" then return end
    if not to_search_val_type or not search_types[to_search_val_type] then return end
    if to_search_val_type == "prop" then
        local literally_everything = {}
        if LP.Character then
            for i2,v2 in pairs(LP.Character:GetDescendants()) do
                if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                    table.insert(literally_everything,v2)
                end
            end
        end
        for i2,v2 in pairs(LP:GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(game:GetService("ReplicatedFirst"):GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(game:GetService("ReplicatedStorage"):GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text")then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(game:GetService("StarterGui"):GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(game:GetService("StarterPack"):GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(game:GetService("CoreGui"):GetDescendants()) do
            if v2:IsDescendantOf(CheatBlox) then continue end
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(game:GetService("Lighting"):GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(workspace:GetDescendants()) do
            if v2.ClassName:sub(-5) == "Value" or v2.ClassName:find("Text") then
                table.insert(literally_everything,v2)
            end
        end
        for i2,v2 in pairs(literally_everything) do
            if v2.ClassName:find("Text") and v2.ClassName ~= "UITextSizeConstraint" and v2.ClassName ~= "Texture" then
                if v2.Text == to_search_val or string.find(string.lower(v2.Text),string.lower(tostring(to_search_val))) then
                    local val_button = CreateButtonLog(v2.Text,Color3.new(0,1,1),"")
                    val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                    val_button.MouseButton1Click:Connect(function()
                        PromptValueChange(v2,"Prop","Text",v2.Text)
                    end)
                    val_button.MouseButton2Click:Connect(function()
                        RightClick(val_button,"Prop",{v2})
                    end)
                    val_button.Parent = ValLogsFrame
                end
            elseif v2.ClassName:sub(-5) == "Value" then
                if v2.Value == to_search_val then
                    local val_button = CreateButtonLog(v2.Value,Color3.new(0,1,1),"")
                    val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                    val_button.MouseButton1Click:Connect(function()
                        PromptValueChange(v2,"Prop","Value",v2.Value)
                    end)
                    val_button.Parent = ValLogsFrame
                end
            end
        end
    else
        if to_search_val_type == "upvalue" then
            for i,v in pairs(getgc()) do
                if type(v) ~= "function" or is_synapse_function(v) or not islclosure(v) then continue end
                for i2,v2 in pairs(debug.getupvalues(v)) do
                    if (v2 == to_search_val) or (type(v2) == "string" and type(to_search_val) == "string" and string.find(string.lower(v2),string.lower(to_search_val))) then
                        local val_button = CreateButtonLog(ValueToString(v2),Color3.new(0,1,1),"")
                        val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                        val_button.MouseButton1Click:Connect(function()
                            PromptValueChange(v,"Upvalue",i2,v2)
                        end)
                        val_button.MouseButton2Click:Connect(function()
                            if type(v2) == "function" then
                                RightClick(val_button,"DebugFunc",{v2,v})
                            else
                                RightClick(val_button,"DebugVal",{v2,v})
                            end
                        end)
                        val_button.Parent = ValLogsFrame
                    end
                end
            end
        elseif to_search_val_type == "constant" then
            for i,v in pairs(getgc()) do
                if type(v) ~= "function" or is_synapse_function(v) or not islclosure(v) then continue end
                for i2,v2 in pairs(debug.getconstants(v)) do
                    if (v2 == to_search_val) or (type(v2) == "string" and type(to_search_val) == "string" and string.find(string.lower(v2),string.lower(to_search_val))) then
                        local val_button = CreateButtonLog(ValueToString(v2),Color3.new(0,1,1),"")
                        val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                        val_button.MouseButton1Click:Connect(function()
                            PromptValueChange(v,"Constant",i2,v2)
                        end)
                        val_button.MouseButton2Click:Connect(function()
                            if type(v2) == "function" then
                                RightClick(val_button,"DebugFunc",{v2,v})
                            else
                                RightClick(val_button,"DebugVal",{v2,v})
                            end
                        end)
                        val_button.Parent = ValLogsFrame
                    end
                end
            end
        elseif to_search_val_type == "proto" then
            for i,v in pairs(getgc()) do
                if type(v) ~= "function" or is_synapse_function(v) or not islclosure(v) then continue end
                for i2,v2 in pairs(debug.getprotos(v)) do
                    if v2 == to_search_val or to_search_val == GetFuncName(debug.getproto(v,i2,true)[1]) then
                        local val_button = CreateButtonLog(ValueToString(v2),Color3.new(0,1,1),"")
                        val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                        val_button.MouseButton1Click:Connect(function()
                            PromptValueChange(v,"Proto",i2,v2)
                        end)
                        val_button.MouseButton2Click:Connect(function()
                            if type(v2) == "function" then
                                RightClick(val_button,"DebugFunc",{v2,v})
                            else
                                RightClick(val_button,"DebugVal",{v2,v})
                            end
                        end)
                        val_button.Parent = ValLogsFrame
                    end
                end
            end
        elseif to_search_val_type == "global" then
            for i,v in pairs(getgc()) do
                if type(v) ~= "function" or is_synapse_function(v) or not islclosure(v) then continue end
                for i2,v2 in pairs(getfenv(v)) do
                    if v2 == to_search_val or (type(v2) == "string" and string.find(string.lower(v2),string.lower(to_search_val))) or (type(i2) == "string" and to_search_val == i2) then
                        local val_button = CreateButtonLog(ValueToString(v2),Color3.new(0,1,1),"")
                        val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                        val_button.MouseButton1Click:Connect(function()
                            PromptValueChange(v,"Global",i2,v2)
                        end)
                        val_button.MouseButton2Click:Connect(function()
                            if type(v2) == "function" then
                                RightClick(val_button,"DebugFunc",{v2,v})
                            else
                                RightClick(val_button,"DebugVal",{v2,v})
                            end
                        end)
                        val_button.Parent = ValLogsFrame
                    end
                end
            end
        elseif to_search_val_type == "table" then
            for i,v in pairs(getgc(true)) do
                if type(v) == "table" and #v < 50 then
                    for i2,v2 in pairs(v) do
                        if v2 == to_search_val then
                            local val_button = CreateButtonLog(ValueToString(v2),Color3.new(0,1,1),"")
                            val_button.Position = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
                            val_button.MouseButton1Click:Connect(function()
                                PromptValueChange(v,"Table",i2,v2)
                            end)
                            val_button.Parent = ValLogsFrame
                        end
                    end
                end
            end
        end
    end
    ValLogsFrame.CanvasSize = UDim2.new(0,0,0,#ValLogsFrame:GetChildren()*20)
end)

EnabledButton.MouseButton1Click:Connect(function()
    Settings["Enabled"] = not Settings["Enabled"]
    EnabledButton.Text = "CheatBlox: "..((Settings["Enabled"] and '<font color="rgb(0,255,0)">ON</font>') or '<font color="rgb(255,0,0)">OFF</font>')
end)

SoundsButton.MouseButton1Click:Connect(function()
    Settings["Sounds"] = not Settings["Sounds"]
    SoundsButton.Text = "Sounds: "..((Settings["Sounds"] and '<font color="rgb(0,255,0)">ON</font>') or '<font color="rgb(255,0,0)">OFF</font>')
end)



---// Finishing //---

do
    local dragging
    local dragInput
    local dragStart
    local startPos

    local update = function(input)
        if not CanClick() then return end
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    MainFrame.InputBegan:Connect(function(input)
        if not CanClick() then return end
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    MainFrame.InputChanged:Connect(function(input)
        if not CanClick() then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if not CanClick() then return end
        if input == dragInput and dragging and MainFrame.Visible then
            update(input)
        end
    end)
end

do
    local dragging
    local dragInput
    local dragStart
    local startPos

    local update = function(input)
        if not CanClick() then return end
        local delta = input.Position - dragStart
        ChangerFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    ChangerFrame.InputBegan:Connect(function(input)
        if not CanClick() then return end
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = ChangerFrame.Position
            
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    ChangerFrame.InputChanged:Connect(function(input)
        if not CanClick() then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if not CanClick() then return end
        if input == dragInput and dragging and ChangerFrame.Visible then
            update(input)
        end
    end)
end

local TweeningButtonColors = {}
MakeCoolButton = function(d)
    if d:IsA("TextButton") then
        d.MouseButton1Click:Connect(function()
            if Settings["Sounds"] then
                ClickSound:Play()
            end
            for i2,v2 in pairs(TweeningButtonColors) do
                if v2 == d then
                    return
                end
            end
            table.insert(TweeningButtonColors,d)
            local newcolors = {d.TextColor3.R-0.4,d.TextColor3.G-0.4,d.TextColor3.G-0.4}
            for i2,v2 in pairs(newcolors) do
                if v2 < 0 then
                    newcolors[i2] = 0
                end
            end
            TweenService:Create(d,TextClickTween,{["TextColor3"] = Color3.new(unpack(newcolors))}):Play()
            wait(0.21)
            for i2,v2 in pairs(TweeningButtonColors) do
                if v2 == d then
                    table.remove(TweeningButtonColors,i2)
                end
            end
        end)
    end
end
UserInputService.InputBegan:Connect(function(Tinfo,bool)
    if bool then return end
    if HideCooldown then return end
    if Tinfo.KeyCode == Enum.KeyCode.F8 then
        HideCooldown = true
        MainFrame.Visible = Hidden
        Hidden = not Hidden
        spawn(function()
            wait(0.2)
            HideCooldown = false
        end)
    end
end)
for i,v in pairs(CheatBlox:GetDescendants()) do
    MakeCoolButton(v)
end



for i,v in pairs(CheatBlox:GetDescendants()) do
    if v:IsA("TextBox") then
        ScriptingBox(v)
    end
end

for i,v in pairs(CheatBlox:GetDescendants()) do
    v.Name = ran_gen(100)
end
CheatBlox.Name = ran_gen(100)
wait()
if gethui and type(gethui) == "function" then
    CheatBlox.Parent = gethui()
else
    CheatBlox.Parent = game:GetService("CoreGui")
end
