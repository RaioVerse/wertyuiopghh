-- Supondo que os NPCs estejam em workspace.NPCs
local pastaNPCs = workspace:FindFirstChild("NPCs") -- troque pelo nome certo

if pastaNPCs then
    for _,npc in ipairs(pastaNPCs:GetChildren()) do
        print(npc.Name)
    end
else
    print("Pasta de NPCs não encontrada!")
end
