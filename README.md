# Sword-roblox

# ⚔️ Sistema de Espada — Roblox (Lua)

Script de una espada equipable con daño, cooldown, protección de auto-daño y animación de ataque, hecho en Roblox Studio con Luau.

## Funcionalidades

- **Daño por golpe**: la espada resta vida al `Humanoid` del objetivo al conectar un golpe.
- **Cooldown de golpe**: evita que un mismo contacto haga daño repetido mientras la espada sigue tocando al objetivo.
- **Protección de auto-daño**: el dueño de la espada nunca se hace daño a sí mismo.
- **Animación de ataque**: al hacer click con la espada equipada (`Activated`), se reproduce una animación de swing.
- **Daño ligado al ataque**: la espada solo hace daño mientras se está ejecutando la animación de swing (no por simple contacto pasivo).

## Cómo funciona

1. `Activated` se dispara cuando el jugador hace click con la espada equipada:
   - Reproduce la animación de swing (`AnimationId` configurable).
   - Activa una ventana de tiempo (`estoyAtacando = true`) durante la cual la espada puede hacer daño.
   - Aplica un cooldown (`puedeAnimacion`) para no poder reactivar el swing de inmediato.

2. `Touched` se dispara cuando la espada toca algo:
   - Solo continúa si `estoyAtacando` es `true` (o sea, si el jugador está en medio de un swing).
   - Verifica que lo tocado tenga un `Humanoid` (sea un personaje).
   - Verifica que no sea el propio dueño de la espada.
   - Aplica el daño, con su propio cooldown (`puedeGolpear`) para no golpear varias veces por segundo mientras la espada sigue en contacto.

## Script

\`\`\`lua
local espada = script.Parent.Handle 
local puedeGolpear = true 
local puedeAnimacion = true
local estoyAtacando = false

script.Parent.Activated:Connect(function()
	if puedeAnimacion == true then
		local humano2 = script.Parent.Parent:FindFirstChild("Humanoid")
		local animador = humano2:FindFirstChild("Animator")
		local animacion = Instance.new("Animation")
		animacion.AnimationId = "rbxassetid://99785740875013"

		local pista = animador:LoadAnimation(animacion)
		pista.Priority = Enum.AnimationPriority.Action
		pista:Play()

		estoyAtacando = true
		puedeAnimacion = false
		task.wait(1.5)
		puedeAnimacion = true
		estoyAtacando = false
	end
end)

espada.Touched:Connect(function(hit)
	local humano = hit.Parent:FindFirstChild("Humanoid")
	if estoyAtacando == true then
		if humano then
			if hit.Parent ~= script.Parent.Parent then 
				if puedeGolpear == true then
					humano:TakeDamage(5)
					puedeGolpear = false
					task.wait(1.5)
					puedeGolpear = true
				end
			end	
		end
	end
end)
\`\`\`

## Requisitos de estructura en Roblox Studio

- `Tool` con una `Part`/`MeshPart` llamada exactamente `Handle`.
- `Script` dentro del `Tool`.
- Animación publicada en Roblox con su `AnimationId` correspondiente.

## Posibles mejoras futuras

- Ajustar el tamaño y modelo visual de la espada.
- Variar el daño según el tipo de golpe.
- Efectos de sonido/visuales al impactar.
