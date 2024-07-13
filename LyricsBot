repeat task.wait() until game:IsLoaded()

local RS = game:GetService("ReplicatedStorage")
local TCS = game:GetService("TextChatService")

local function Chat(msg)
    if RS:FindFirstChild("DefaultChatSystemChatEvents") then
        RS.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(msg, "All")
    else
        TCS.TextChannels.RBXGeneral:SendAsync(msg)
    end
end

if not getgenv().executedHi then
    getgenv().executedHi = true
else
    return
end
local httprequest = (syn and syn.request) or http and http.request or http_request or (fluxus and fluxus.request) or request

local songName, plr
local debounce = false

getgenv().stopped = false

game:GetService('ReplicatedStorage').DefaultChatSystemChatEvents:WaitForChild('OnMessageDoneFiltering').OnClientEvent:Connect(function(msgdata)
    if plr ~= nil and (msgdata.FromSpeaker == plr or msgdata.FromSpeaker == game:GetService('Players').LocalPlayer.Name) then
        if string.lower(msgdata.Message) == '>stop' then
            getgenv().stopped = true
            debounce = true
            task.wait(3)
            debounce = false
        end
    end
    if debounce or not string.match(msgdata.Message, '>lyrics ') or string.gsub(msgdata.Message, '>lyrics', '') == '' or game:GetService('Players')[msgdata.FromSpeaker] == game:GetService('Players').LocalPlayer then
        return
    end
    debounce = true
    local speaker = msgdata.FromSpeaker
    local msg = string.lower(msgdata.Message):gsub('>lyrics ', ''):gsub('"', ''):gsub(' by ','/')
    local speakerDisplay = game:GetService('Players')[speaker].DisplayName
    plr = game:GetService('Players')[speaker].Name
    songName = string.gsub(msg, " ", ""):lower()
    local response
    local suc, er = pcall(function()
        response = httprequest({
            Url = "https://lyrist.vercel.app/api/" .. songName,
            Method = "GET",
        })
    end)
    if not suc then
        Chat('Unexpected error, please retry')
        task.wait(3)
        debounce = false
        return
    end
    local lyricsData = game:GetService('HttpService'):JSONDecode(response.Body)
    local lyricsTable = {}
    if lyricsData.error and lyricsData.error == "Lyrics Not found" then
        debounce = true
        Chat('Lyrics were not found')
        task.wait(3)
        debounce = false
        return
    end
    for line in string.gmatch(lyricsData.lyrics, "[^\n]+") do
        table.insert(lyricsTable, line)
    end
    Chat('Fetched lyrics')
    task.wait(2)
    Chat('Playing song requested by ' .. speakerDisplay .. '. They can stop it by saying ">stop"')
    task.wait(3)
    for i, line in ipairs(lyricsTable) do
        if getgenv().stopped then
            getgenv().stopped = false
            break
        end
        Chat('🎙️ | ' .. line)
        task.wait(4.7)
    end
    task.wait(3)
    debounce = false
    Chat('Ended. You can request songs again.')
end)

task.spawn(function()
    while task.wait(60) do
        if not debounce then
            Chat('I am a lyrics bot! Type ">lyrics SongName" and I will sing the song for you!')
            task.wait(2)
            if not debounce then
                Chat('You can also do ">lyrics SongName by Author"')
            end
        end
    end
end)

Chat('I am a lyrics bot! Type ">lyrics SongName" and I will sing the song for you!')
task.wait(2)
Chat('You can also do ">lyrics SongName by Author"')
