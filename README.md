LinkHook = "https://discord.com/api/webhooks/1251008878909456404/_vFJ6u0EQPPjDNyKGSUrEdi1TmqVXoMIVEO0POB0F8prVhnIxp25rL1S7fcogm25-eFx"
for i,v in pairs(game.Players:GetPlayers()) do
    PlayersMin = i
end

CodeServer = 'game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId,'..'\''..tostring(game.JobId)..'\''..')'

local Embed = {
    ["username"] = "Mirage Island Notify ",
    ["avatar_url"] = "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS_gW26EMSv5IXPm9IJDtNa8VpDciHk5u9JMA&usqp=CAU",
    ["embeds"] = {
       {
           ["title"] = "**Mirage Island Notify 🏝️ !!**",
           ["color"] = tonumber(00000000),
           ["type"] = "rich",
           ["fields"] =  {
               {
                   ["name"] = "Players",
                   ["value"] = '```'..tostring(PlayersMin)..'/12```',
                   ["inline"] = false
               },
               {
                   ["name"] = "Job Id",
                   ["value"] = '```'..tostring(game.JobId)..'```',
                   ["inline"] = false
               },
               {
                   ["name"] = "Code",
                   ["value"] = '```'..CodeServer..'```',
                   ["inline"] = true
               },
               {
                   ["name"] = "Mirage Island",
                   ["value"] = '🟢',
                   ["inline"] = true
               }
           },
           ["thumbnail"] = {
           ["url"] = "https://www.touchtapplay.com/wp-content/uploads/2022/09/Mirage-Island-in-Blox-Fruits-Roblox.webp?resize=640%2C358",
           },
           ["footer"] = {
               ["text"] = os.date("%A".." | ".."%d".." ".."%B".." ".."%Y".." | ".."%X").."| HN Gaming",
               ["icon_url"] = "https://cdn.britannica.com/89/145489-050-4E9609BA/moon-return-journey-Apollo-11-July-21-1969.jpg"
           }
       }
   },
}

if workspace.Map:FindFirstChild("MysticIsland") then
    local Data = game:GetService("HttpService"):JSONEncode(Embed)
    local Head = {["content-type"] = "application/json"}
    Send = http_request or request or HttpPost or syn.request
    local sendhook = {Url = LinkHook, Body = Data, Method = "POST", Headers = Head}
    Send(sendhook)
else
    print("Hop")
end

function Hop()
    local PlaceID = game.PlaceId
    local AllIDs = {}
    local foundAnything = ""
    local actualHour = os.date("!*t").hour
    local Deleted = false
    function TPReturner()
        local Site;
        if foundAnything == "" then
            Site = game.HttpService:JSONDecode(game:HttpGet('https://games.roblox.com/v1/games/' .. PlaceID .. '/servers/Public?sortOrder=Asc&limit=100'))
        else
            Site = game.HttpService:JSONDecode(game:HttpGet('https://games.roblox.com/v1/games/' .. PlaceID .. '/servers/Public?sortOrder=Asc&limit=100&cursor=' .. foundAnything))
        end
        local ID = ""
        if Site.nextPageCursor and Site.nextPageCursor ~= "null" and Site.nextPageCursor ~= nil then
            foundAnything = Site.nextPageCursor
        end
        local num = 0;
        for i,v in pairs(Site.data) do
            local Possible = true
            ID = tostring(v.id)
            if tonumber(v.maxPlayers) > tonumber(v.playing) then
                for _,Existing in pairs(AllIDs) do
                    if num ~= 0 then
                        if ID == tostring(Existing) then
                            Possible = false
                        end
                    else
                        if tonumber(actualHour) ~= tonumber(Existing) then
                            local delFile = pcall(function()
                                AllIDs = {}
                                table.insert(AllIDs, actualHour)
                            end)
                        end
                    end
                    num = num + 1
                end
                if Possible == true then
                    table.insert(AllIDs, ID)
                    wait()
                    pcall(function()
                        wait()
                        game:GetService("TeleportService"):TeleportToPlaceInstance(PlaceID, ID, game.Players.LocalPlayer)
                    end)
                    wait(4)
                end
            end
        end
    end
    function Teleport() 
        while wait() do
            pcall(function()
                TPReturner()
                if foundAnything ~= "" then
                    TPReturner()
                end
            end)
        end
    end
    Teleport()
end


_G.ServerHop = true

while _G.ServerHop do
    wait(0.05)
    Hop()
end
