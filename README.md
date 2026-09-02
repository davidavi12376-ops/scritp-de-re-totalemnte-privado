-- Tela preta com Fade Out
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local LP = Players.LocalPlayer
local PG = LP:WaitForChild("PlayerGui")

-- Remove se já existir
pcall(function()
	local old = PG:FindFirstChild("TelaPretaMsg")
	if old then old:Destroy() end
end)

local Gui = Instance.new("ScreenGui")
Gui.Name = "TelaPretaMsg"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.DisplayOrder = 999
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Gui.Parent = PG

-- Fundo preto (começa transparente)
local Black = Instance.new("Frame")
Black.Size = UDim2.fromScale(1, 1)
Black.Position = UDim2.fromScale(0, 0)
Black.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Black.BackgroundTransparency = 1
Black.BorderSizePixel = 0
Black.ZIndex = 10
Black.Parent = Gui

-- Mensagem (também começa transparente)
local Msg = Instance.new("TextLabel")
Msg.Size = UDim2.new(0.9, 0, 0.3, 0)
Msg.Position = UDim2.new(0.05, 0, 0.35, 0)
Msg.BackgroundTransparency = 1
Msg.Text = "o sky e eu decidimos n passar\nporque vcs e curioso demais\ne n reclama...."
Msg.TextColor3 = Color3.fromRGB(255, 255, 255)
Msg.TextTransparency = 1
Msg.TextSize = 28
Msg.Font = Enum.Font.GothamBold
Msg.TextWrapped = true
Msg.TextXAlignment = Enum.TextXAlignment.Center
Msg.TextYAlignment = Enum.TextYAlignment.Center
Msg.ZIndex = 11
Msg.Parent = Black

-- Fade Out (fica preto aos poucos)
local fadeInfo = TweenInfo.new(2.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

local tweenBlack = TweenService:Create(Black, fadeInfo, {
	BackgroundTransparency = 0
})

local tweenText = TweenService:Create(Msg, fadeInfo, {
	TextTransparency = 0
})

tweenBlack:Play()
tweenText:Play()

print("Fade out iniciado...")
