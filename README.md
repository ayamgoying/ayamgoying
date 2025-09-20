-- https://scriptblox.com/script/Brookhaven-RP-Play-piano-sheets-46474


if getgenv().ExecutedRight then return end
getgenv().ExecutedRight = true
task.spawn(function()
	local CoreGui = cloneref(game:GetService("CoreGui"))
	local TweenService = game:GetService("TweenService")

	local screenGui = Instance.new("ScreenGui")
	screenGui.Name = "FullscreenLoading"
	screenGui.IgnoreGuiInset = true
	screenGui.Parent = CoreGui

	local bg = Instance.new("Frame")
	bg.BackgroundColor3 = Color3.new(0, 0, 0)
	bg.BackgroundTransparency = 1
	bg.BorderSizePixel = 0
	bg.Size = UDim2.new(1, 0, 1, 0)
	bg.Position = UDim2.new(0, 0, 0, 0)
	bg.Parent = screenGui

	local text = Instance.new("TextLabel")
	text.BackgroundTransparency = 1
	text.Text = "loading"
	text.Size = UDim2.new(1, 0, 1, 0)
	text.Position = UDim2.new(0, 0, 0, 0)
	text.Font = Enum.Font.GothamBold
	text.TextColor3 = Color3.new(1, 1, 1)
	text.TextTransparency = 1
	text.TextScaled = true
	text.TextStrokeTransparency = 0.6
	text.Parent = bg

	TweenService:Create(bg, TweenInfo.new(1), {BackgroundTransparency = 0}):Play()
	TweenService:Create(text, TweenInfo.new(1), {TextTransparency = 0}):Play()

	local running = true
	task.spawn(function()
		local dots = ""
		while running do
			dots = dots == "..." and "" or dots .. "."
			text.Text = "loading" .. dots
			task.wait(0.5)
		end
	end)

	task.spawn(function()
	repeat task.wait() until getgenv().AllNotesFinished
		running = false
		TweenService:Create(bg, TweenInfo.new(1), {BackgroundTransparency = 1}):Play()
		TweenService:Create(text, TweenInfo.new(1), {TextTransparency = 1}):Play()
		task.wait(1)
		screenGui:Destroy()
end)
end)
local Players = game:GetService("Players")
local rs, ws = game:GetService("ReplicatedStorage"), workspace
local player = Players.LocalPlayer
getgenv().Holdingts = false
local PianoNotes = {
["C2"] = "rbxassetid://111842008687926", ["C#2"] = "rbxassetid://70981692272966", ["D2"] = "rbxassetid://139380394259973",
["D#2"] = "rbxassetid://93444915027584", ["E2"] = "rbxassetid://87629114895960", ["F2"] = "rbxassetid://73972689087289",
["F#2"] = "rbxassetid://70376334727828", ["G2"] = "rbxassetid://100837629997022", ["G#2"] = "rbxassetid://136188639889438",
["A2"] = "rbxassetid://71752018166979", ["A#2"] = "rbxassetid://120262398697016", ["B2"] = "rbxassetid://110455028643359",
["C3"] = "rbxassetid://123112876875374", ["C#3"] = "rbxassetid://120923365636009", ["D3"] = "rbxassetid://106306658545729",
["D#3"] = "rbxassetid://124841365961522", ["E3"] = "rbxassetid://82335077730546", ["F3"] = "rbxassetid://139732365240441",
["F#3"] = "rbxassetid://83552558259422", ["G3"] = "rbxassetid://137224813797993", ["G#3"] = "rbxassetid://131726792016780",
["A3"] = "rbxassetid://95091023271668", ["A#3"] = "rbxassetid://112380265050214", ["B3"] = "rbxassetid://105256060658131",
["C4"] = "rbxassetid://78413131279275", ["C#4"] = "rbxassetid://72998150378307", ["D4"] = "rbxassetid://105581334194673",
["D#4"] = "rbxassetid://86516725681894", ["E4"] = "rbxassetid://132881598286319", ["F4"] = "rbxassetid://133575380950758",
["F#4"] = "rbxassetid://126196495975202", ["G4"] = "rbxassetid://114434657963330", ["G#4"] = "rbxassetid://133080223235416",
["A4"] = "rbxassetid://136348068040461", ["A#4"] = "rbxassetid://128661535499577", ["B4"] = "rbxassetid://128462707908914",
["C5"] = "rbxassetid://121997668450827", ["C#5"] = "rbxassetid://121413839180669", ["D5"] = "rbxassetid://90740749235134",
["D#5"] = "rbxassetid://113030261877109", ["E5"] = "rbxassetid://140406182404884", ["F5"] = "rbxassetid://119631153742000",
["F#5"] = "rbxassetid://70828942309686", ["G5"] = "rbxassetid://131269431485825", ["G#5"] = "rbxassetid://118262323318899",
["A5"] = "rbxassetid://125853393278154", ["A#5"] = "rbxassetid://99745152176175", ["B5"] = "rbxassetid://138660648674060",
["C6"] = "rbxassetid://110812706317973", ["C#6"] = "rbxassetid://104201273760781", ["D6"] = "rbxassetid://129569432159919",
["D#6"] = "rbxassetid://77417182913309", ["E6"] = "rbxassetid://91804565247146", ["F6"] = "rbxassetid://74247761440555",
["F#6"] = "rbxassetid://106884926622420", ["G6"] = "rbxassetid://107760096922427", ["G#6"] = "rbxassetid://90865662835904",
["A6"] = "rbxassetid://134133616625207", ["A#6"] = "rbxassetid://79684302895378", ["B6"] = "rbxassetid://93034533215278",
["C7"] = "rbxassetid://125973710014979",
}
local NoteMapping = {
	"1","!","2","@","3","4","$","5","%","6","^","7","8","*","9","(","0",
	"q","Q","w","W","e","E","r","t","T","y","Y","u","i","I","o","O","p",
	"P","a","s","S","d","D","f","g","G","h","H","j","J","k","l","L","z",
	"Z","x","c","C","v","V","b","B","n","m"
}
local KeysToNotes = {}
do
	local notes = {}
	for note in pairs(PianoNotes) do table.insert(notes, note) end
	table.sort(notes, function(a, b)
		local pitch = {[ "C"]=0,["C#"]=1,["D"]=2,["D#"]=3,["E"]=4,["F"]=5,["F#"]=6,["G"]=7,["G#"]=8,["A"]=9,["A#"]=10,["B"]=11}
		local function getVal(n)
			local note, octave = n:match("([A-G]#?)(%d+)")
			return tonumber(octave)*12 + pitch[note]
		end
		return getVal(a) < getVal(b)
	end)
	for i,n in ipairs(NoteMapping) do KeysToNotes[n] = notes[i] end
end
local gui = Instance.new("ScreenGui", cloneref(game:GetService("CoreGui")) or game:GetService("CoreGui") or game.Players.LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "PianoGUI"
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 400, 0, 340)
main.Position = UDim2.new(0.5, -200, 0.5, -170)
main.BackgroundColor3 = Color3.fromRGB(40,40,40)
main.BorderSizePixel = 0
main.Name = "MainFrame"
main.Draggable = true
main.Active = true
main.Visible = false
local title = Instance.new("TextLabel", main)
title.Text = "ts so piano gng 💔"
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(60,60,60)
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 20
local sheetBox = Instance.new("TextBox", main)
sheetBox.Size = UDim2.new(1, -20, 0, 180)
sheetBox.Position = UDim2.new(0, 10, 0, 40)
sheetBox.TextWrapped = true
sheetBox.TextXAlignment = Enum.TextXAlignment.Left
sheetBox.TextYAlignment = Enum.TextYAlignment.Top
sheetBox.MultiLine = true
sheetBox.ClearTextOnFocus = false
sheetBox.Text = [[
[id] [pf] [sh] [pj] [sh] [pf] [id] [pf] [sh] [pj] [sh] [pf]  [id] [pf] [sh] [pj] [sh] [pf] [id] [pf] [sh] [pj] [sh] [pf]  [us] [of] [ah] [oj] [ah] [of] [us] [of] [ah] [oj] [ah] [of]  [us] [of] [ah] [oj] [ah] [of] [Ys] [of] [ah] [oj] [ah] [of]  [ys] [id] [pg] [ih] [pg] [id] [ys] [id] [pg] [ih] [pg] [id]  [ys] [id] [pg] [ih] [pg] [id] [ys] [id] [pg] [ih] [pg] [id]  [ta] [us] [od] [uf] [od] [us] [ta] [us] [od] [uf] [od] [us]  [ta] [us] [od] [uf] [od] [us] [ta] [us] [od] [uf] [od] [us]  [if] [pf] [sf] [pf] [sf] [pd] [ix] p s p s p  [if] [pf] [sf] [pf] [sf] p [if] [pf] [sf] [pf] [sf] p  [uf] [of] [af] [of] [af] [od] [ux] o a o a o  [uf] [of] [af] [of] [af] o [Yf] [of] [af] [of] [af] o  [yd] [id] [pf] [id] [pf] [id] [yx] i p i p i  [yd] [id] [pf] [id] [pf] [id] [yx] i p i p i  [tf] [ud] [of] [ud] [of] u [tf] [ud] [of] [ud] [of] u  [tf] [ud] [of] u [od] [us] t [uf] [oh]  [4psfj] [qet] [pj] [ak] [sl]  4 [qet] [pj] [sl]  [3ak] [pj] [oh] [0wroh] [uf]  [3ak] [pj] [oh] [0wrak] [pj] [oh]  [2oh] [ipsg] [9qe]  2 [ts] [9qeoh] [ig] [uf]  [3uf] 3 3 [3ep] 3 3  [3ts] 3 3 [yd]  [4upsf] 4 8 q 8 [ed] [8f] [qh] [8j] [qh] [8f]  [4d] [8f] [qh] [8j] [qh] [8f] [ed] 8 q 8 q 8  [4ep] 8 q 8 q 8 [era] 8 q 8 q 8  [4oah] 8 q 8 q 8 [eipg] 8 q 8 q  [1uof] 1 5 8 5 [0a] [5s] [8d] [5f] [8d] [5s]  [1a] [5s] [8d] [5f] [8d] [5s] [0a] 5 [8ep] 5 [8ts] [5ra]  1 5 8 5 8 5 [0ra] 5 8 5 8 5  [2ts] 6 9 6 9 6 [3yd] 7 0 7 0  [4upsf] 4 8 q 8 [ed] [8f] [qh] [8j] [qh] [8f]  [4d] [8f] [qh] [8j] [qh] [8f] [ed] 8 q 8 q 8  [4pj] 8 q 8 q 8 [eak] 8 q 8 q 8  [4hkv] 8 q 8 q 8 [egjc] 8 q 8 q  [1fhx] 1 5 8 5 [0a] [5s] [8d] [5f] [8d] [5s]  [1a] [5s] [8d] [5f] [8d] [5s] [0a] 5 [8pj] 5 [8sl] [5ak]  1 5 8 5 8 5 [0a] [5s] [8d] [5f] [8d] [5s]  [2a] [6s] [9d] [6f] [9d] [6s] [3a] [7s] [0d] [7f] [0d] [7s]  [id] [pf] [sf] [pf] [sf] [pf] [ix] p s p s p  [id] [pf] [sf] [pf] [sf] [pf] [id] [pf] [sf] [pf] [sf] [pf]  [ud] [of] [af] [of] [af] [of] [ux] o a o a o  [ud] [of] [af] [of] [af] o [Yd] [of] [af] [of] [af] o  [yd] [if] [pf] [id] [pf] [id] [yx] i p i p i  [yd] [id] [pf] [id] [pf] [id] [yx] i p i p i  [tf] [ud] [of] [ud] [os] u [tf] [ud] [of] [ud] [os] u  [tf] [ud] [of] u [od] [us] t [uf] [oh]  [4psfj] 4 8 q e t [pj] [ak] [sl]  4 4 8 q e t [pj] [sl]  [3ak] [3pj] 7 [0oh] w [roh] [uf]  [3ak] [3pj] 7 [0oh] [@ak] [pj] [oh]  [2oh] [2ipg] 6 9 q e  2 2 6 [9ts] q [eoh] [ig] [uf] [uf]  1 1 5 8 0 w  [1t] y [1u] 5 [8y] [0t] w [uf] [oh]  [4pj] [qet] [pj] [ak] [sl]  4 [qet] [pj] [sl]  [3ak] [pj] [oah] [0wroah] [uf]  [3ak] [ak] [ak] [@sl] [oh] [oh]  [2oh] [ig] [9qe]  2 [ts] [9qeoh] [ig] [uf]  [3uf] 3 3 [3ep] 3 3  [3ts] 3 3 [yd]  [4upsf] 4 8 q 8 [ed] [8f] [qh] [8j] [qh] [8f]  [4d] [8f] [qh] [8j] [qh] [8f] [ed] 8 q 8 q 8  [4ep] 8 q 8 q 8 [era] 8 q 8 q 8  [4oadh] 8 q 8 q 8 [eipsg] 8 q 8 q  [1uoaf] 1 5 8 5 [0a] [5s] [8d] [5f] [8d] [5s]  [1a] [5s] [8d] [5f] [8d] [5s] [0a] 5 [8ep] 5 [8ts] [5ra]  1 5 8 5 8 5 [0ra] 5 8 5 8 5  [2ts] 6 9 6 9 6 [3yd] 7 0 7 0  [4upsf] 4 8 q 8 [ed] [8f] [qh] [8j] [qh] [8f]  [4d] [8f] [qh] [8j] [qh] [8f] [ed] 8 q 8 q 8  [4ep] 8 q 8 q 8 [era] 8 q 8 q 8  [4oadh] 8 q 8 q 8 [eipsg] 8 q 8 q  [1uoaf] 1 5 8 5 [0a] [5s] [8d] [5f] [8d] [5s]  [1a] [5s] [8d] [5f] [8d] [5s] [0a] 5 [8pj] 5 [8sl] 5  [1ak] 5 8 5 8 5 0 5 8 5 8 5  [2a] 6 9 6 9 6 [3d] 7 0 7 0  [qy] u e t i t e  q e t i t e  [qp] e t [ia] t e  [qh] e t [ig] t e  [8f] 0 w t w 0  8 0 w t w 0  [8a] s [0d] f [wd] s [ta] w 0  [8s] 0 w [td] w 0  [4qf] [qet]  [4qp] [qeta]  [eohv] [eigc]  [18fx] [80w]  [pj] [sl]  [18ak] [80w]  [qetf] s  [ip]  [0wrf] a  [oa]  [QS9e] p  O u  [S80w] p  O  [Q9ea] I  a z  [Sqe] i y p  a O S g S  H
]]
sheetBox.Font = Enum.Font.Code
sheetBox.TextSize = 16
sheetBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
sheetBox.TextColor3 = Color3.new(1,1,1)
local tempoLabel = Instance.new("TextLabel", main)
tempoLabel.Size = UDim2.new(0, 60, 0, 25)
tempoLabel.Position = UDim2.new(0, 10, 0, 230)
tempoLabel.Text = "Tempo:"
tempoLabel.TextColor3 = Color3.new(1,1,1)
tempoLabel.BackgroundTransparency = 1
tempoLabel.Font = Enum.Font.SourceSans
tempoLabel.TextSize = 18
local tempoBox = Instance.new("TextBox", main)
tempoBox.Size = UDim2.new(0, 60, 0, 25)
tempoBox.Position = UDim2.new(0, 80, 0, 230)
tempoBox.Text = "240"
tempoBox.Font = Enum.Font.SourceSans
tempoBox.TextSize = 18
tempoBox.BackgroundColor3 = Color3.fromRGB(60,60,60)
tempoBox.TextColor3 = Color3.new(1,1,1)
tempoBox.ClearTextOnFocus = false
local function makeBtn(txt, xPos)
local b = Instance.new("TextButton", main)
b.Size = UDim2.new(0, 100, 0, 30)
b.Position = UDim2.new(0, xPos, 1, -40)
b.Text = txt
b.Font = Enum.Font.SourceSans
b.TextSize = 18
b.BackgroundColor3 = Color3.fromRGB(80,80,80)
b.TextColor3 = Color3.new(1,1,1)
return b
end
local playBtn = makeBtn("Play", 10)
local pauseBtn = makeBtn("Pause", 150)
local stopBtn = makeBtn("Stop", 290)
local statusLabel = Instance.new("TextLabel", main)
statusLabel.Size = UDim2.new(1, -20, 0, 25)
statusLabel.Position = UDim2.new(0, 10, 0, 270)
statusLabel.BackgroundTransparency = 1
statusLabel.Font = Enum.Font.GothamBold
statusLabel.TextSize = 18
statusLabel.TextColor3 = Color3.new(1, 1, 1)
statusLabel.Text = "Checking gun..."
local playing, paused = false, false
local toggleshots = false
local function playserver(noteid)
	toggleshots = not toggleshots -- toggling shots making it 2x faster ig?
	local char = game:GetService("Players").LocalPlayer.Character
	if not char then return end
	local tool = char:FindFirstChild("Assault")
	if not tool then return end
	local rs, ws = game:GetService("ReplicatedStorage"), workspace

	if toggleshots then
		local m = tool
		local a = {
			ws:WaitForChild("Model", 9e9):GetChildren()[154]:GetChildren()[45],
			m:WaitForChild("Handle", 9e9),
			Vector3.new(0.2, 0.3, -2.5),
			Vector3.new(16.8228, 0.025, -43.4795),
			m:WaitForChild("GunScript_Local", 9e9):WaitForChild("MuzzleEffect", 9e9),
			m:WaitForChild("GunScript_Local", 9e9):WaitForChild("HitEffect", 9e9),
			noteid, noteid,
			{ false },
			{ 25, Vector3.new(0.25, 0.25, 100), BrickColor.new("Bright yellow"), 0.25, Enum.Material.SmoothPlastic, 0.25 },
			true, false
		}
		rs:WaitForChild("RE", 9e9):WaitForChild("1Gu1n", 9e9):FireServer(unpack(a))
	else
		local args = {
			tool:WaitForChild("Handle", 9e9),
			noteid,
			1
		}
		rs:WaitForChild("RE", 9e9):WaitForChild("1Gu1nSound1s", 9e9):FireServer(unpack(args))
	end
end

local function Playaudio(note)
	if typeof(note) == "string" then
		local id = PianoNotes[note]
		if id then
			playserver(string.gsub(id, "rbxassetid://", ""))
			local sound = Instance.new("Sound", workspace)
			sound.SoundId = id
			sound.Volume = 1
			sound:Play()
			game.Debris:AddItem(sound, 2)
		end
	end
end

local function PlaySheet(sheet, tempo)
	local beat = 60 / ((tempo or 120) * 4)
	local i = 1
	local myPlaybackID = tick()
	getgenv().CurrentPlaybackID = myPlaybackID
	getgenv().AllNotesFinished = false
	playing, paused = true, false
	pauseBtn.Text = "Pause"

	while i <= #sheet and playing and getgenv().CurrentPlaybackID == myPlaybackID do
		if paused then repeat task.wait() until not paused or not playing end
		if getgenv().CurrentPlaybackID ~= myPlaybackID then break end

		local char = sheet:sub(i, i)
		if char == "[" then
			local close = sheet:find("]", i)
			if close then
				local group = sheet:sub(i + 1, close - 1)
				for c in group:gmatch(".") do
					if getgenv().CurrentPlaybackID ~= myPlaybackID then break end
					local note = KeysToNotes[c]
					if note then Playaudio(note) end
					task.wait(0.05)
				end
				task.wait(beat)
				i = close
			end
		elseif char == " " then
			task.wait(beat)
		elseif char == "-" then
			task.wait(beat * 0.5)
		else
			local note = KeysToNotes[char]
			if note then Playaudio(note) end
			task.wait(beat)
		end

		i += 1
	end

	playing = false
	getgenv().AllNotesFinished = true
	pauseBtn.Text = "Pause"
end

playBtn.MouseButton1Click:Connect(function()

	getgenv().CurrentPlaybackID = tick()
	playing, paused = false, false
	pauseBtn.Text = paused and "Resume" or " Pause"
	repeat task.wait() until getgenv().AllNotesFinished

	local tempo = tonumber(tempoBox.Text) or 240
	task.spawn(function()
		PlaySheet(sheetBox.Text, tempo)
	end)
end)

pauseBtn.MouseButton1Click:Connect(function()
	if playing then
		paused = not paused
		pauseBtn.Text = paused and "Resume" or " Pause"
	end
end)

stopBtn.MouseButton1Click:Connect(function()
	playing, paused = false, false
end)

task.spawn(function()
	while true do
		local tool = player.Character and player.Character:FindFirstChildOfClass("Tool")
		if tool and tool.Name:lower():find("assault") then
			statusLabel.Text = "working, make sure to wait untill game loaded"
			statusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
			getgenv().Holdingts = true
		else
			statusLabel.Text = "Please hold Assault gun for FE"
			statusLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
			getgenv().Holdingts = false
		end
		task.wait(0.25)
	end
end)


while not getgenv().Holdingts do
local args = {
	"PickingTools",
	"Assault"
}
game:GetService("ReplicatedStorage"):WaitForChild("RE"):WaitForChild("1Too1l"):InvokeServer(unpack(args))

task.wait(1)


local player = game:GetService("Players").LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local backpack = player:WaitForChild("Backpack")
local assault = backpack:WaitForChild("Assault")
assault.Parent = char
task.wait(5)
end

local function playAllNotes()
	getgenv().AllNotesFinished = false
	local notes = {}
	for _, note in pairs(PianoNotes) do
		table.insert(notes, note)
	end

	playing = true
	for name, asset in pairs(PianoNotes) do
		if not playing then break end
		Playaudio(name)
		task.wait(0.1)
	end

	playing = false
	getgenv().AllNotesFinished = true
end

if getgenv().Holdingts then
task.wait(5)
playAllNotes()
end

if getgenv().AllNotesFinished then
	main.Visible = true
end
