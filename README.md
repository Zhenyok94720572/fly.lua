local RS = game:GetService("RunService")
local Players = game:GetService("Players")
local p = Players.LocalPlayer

local function setupGUI()
    if not p.PlayerGui:FindFirstChild("FlyGUI") then
        local sg = Instance.new("ScreenGui")
        sg.Name = "FlyGUI"
        sg.Parent = p.PlayerGui
        
        local fb = Instance.new("TextButton")
        fb.Name = "FlyButton"
        fb.Size = UDim2.new(0,100,0,40)
        fb.Position = UDim2.new(0.02,0,0.02,0)
        fb.Text = "FLY"
        fb.BackgroundColor3 = Color3.fromRGB(0,80,180)
        fb.TextColor3 = Color3.fromRGB(255,255,255)
        fb.Font = Enum.Font.GothamBold
        fb.TextSize = 18
        fb.Parent = sg
        
        local corner1 = Instance.new("UICorner")
        corner1.CornerRadius = UDim.new(0,12)
        corner1.Parent = fb
        
        local up = Instance.new("TextButton")
        up.Name = "UpButton"
        up.Size = UDim2.new(0,70,0,70)
        up.Position = UDim2.new(0.85,0,0.6,0)
        up.Text = "▲"
        up.TextSize = 50
        up.BackgroundColor3 = Color3.fromRGB(0,60,140)
        up.TextColor3 = Color3.fromRGB(255,255,255)
        up.Font = Enum.Font.SourceSansBold
        up.TextStrokeColor3 = Color3.fromRGB(255,255,255)
        up.TextStrokeTransparency = 0.3
        up.Visible = false
        up.Parent = sg
        
        local corner2 = Instance.new("UICorner")
        corner2.CornerRadius = UDim.new(1,0)
        corner2.Parent = up
        
        local down = Instance.new("TextButton")
        down.Name = "DownButton"
        down.Size = UDim2.new(0,70,0,70)
        down.Position = UDim2.new(0.85,0,0.8,0)
        down.Text = "▼"
        down.TextSize = 50
        down.BackgroundColor3 = Color3.fromRGB(0,60,140)
        down.TextColor3 = Color3.fromRGB(255,255,255)
        down.Font = Enum.Font.SourceSansBold
        down.TextStrokeColor3 = Color3.fromRGB(255,255,255)
        down.TextStrokeTransparency = 0.3
        down.Visible = false
        down.Parent = sg
        
        local corner3 = Instance.new("UICorner")
        corner3.CornerRadius = UDim.new(1,0)
        corner3.Parent = down
    end
    return p.PlayerGui.FlyGUI
end

local function main()
    local c = p.Character or p.CharacterAdded:Wait()
    local h = c:WaitForChild("Humanoid")
    local hrp = c:WaitForChild("HumanoidRootPart")
    
    local fly = false
    local bv
    local upP, downP = false, false
    
    local gui = setupGUI()
    local fb = gui.FlyButton
    local up = gui.UpButton
    local down = gui.DownButton
    
    local function toggle()
        fly = not fly
        if fly then
            fb.Text = "STOP"
            fb.BackgroundColor3 = Color3.fromRGB(180,40,40)
            up.Visible = true
            down.Visible = true
            
            bv = Instance.new("BodyVelocity", hrp)
            bv.MaxForce = Vector3.new(0,10000,0)
            
            if p.PlayerGui:FindFirstChild("TouchGui") then
                local tg = p.PlayerGui.TouchGui
                if tg:FindFirstChild("TouchControlFrame") then
                    local tcf = tg.TouchControlFrame
                    if tcf:FindFirstChild("JumpButton") then
                        tcf.JumpButton.Visible = false
                    end
                end
            end
        else
            fb.Text = "FLY"
            fb.BackgroundColor3 = Color3.fromRGB(0,80,180)
            up.Visible = false
            down.Visible = false
            
            if bv then bv:Destroy() end
            
            if p.PlayerGui:FindFirstChild("TouchGui") then
                local tg = p.PlayerGui.TouchGui
                if tg:FindFirstChild("TouchControlFrame") then
                    local tcf = tg.TouchControlFrame
                    if tcf:FindFirstChild("JumpButton") then
                        tcf.JumpButton.Visible = true
                    end
                end
            end
        end
    end
    
    fb.MouseButton1Click:Connect(toggle)
    
    up.MouseButton1Down:Connect(function() upP = true end)
    up.MouseButton1Up:Connect(function() upP = false end)
    down.MouseButton1Down:Connect(function() downP = true end)
    down.MouseButton1Up:Connect(function() downP = false end)
    
    RS.RenderStepped:Connect(function()
        if not fly then return end
        if bv then
            local v = 0
            if upP then v = 20 end
            if downP then v = -20 end
            bv.Velocity = Vector3.new(0,v,0)
        end
    end)
    
    p.CharacterAdded:Connect(function(newChar)
        c = newChar
        h = newChar:WaitForChild("Humanoid")
        hrp = newChar:WaitForChild("HumanoidRootPart")
        if fly then
            toggle()
            task.wait(0.1)
            toggle()
        end
    end)
    
    h.Died:Connect(function()
        if fly then
            toggle()
        end
    end)
end

main()
