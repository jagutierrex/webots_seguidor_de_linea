# e_puck_line_follower.py
from controller import Robot, Motor, DistanceSensor

# Crea una instancia del robot
robot = Robot()

# Obtén el tiempo de paso de la simulación
timestep = int(robot.getBasicTimeStep())

# Obtén los motores de las ruedas
left_motor = robot.getDevice('left wheel motor')
right_motor = robot.getDevice('right wheel motor')

# Establece la velocidad máxima para los motores
left_motor.setVelocity(0.0)
right_motor.setVelocity(0.0)

# Habilita la posición infinita para los motores (modo de velocidad)
left_motor.setPosition(float('inf'))
right_motor.setPosition(float('inf'))

# Obtén los sensores de distancia (usados como sensores de luz IR para la línea)
# Los e-puck tienen 8 sensores, aquí usaremos los dos frontales exteriores para detectar la línea
# ps0 es el sensor frontal derecho, ps7 es el sensor frontal izquierdo
ps0 = robot.getDevice('ps0') # Sensor IR delantero derecho
ps7 = robot.getDevice('ps7') # Sensor IR delantero izquierdo

# Habilita los sensores
ps0.enable(timestep)
ps7.enable(timestep)

# Velocidad base del robot
base_speed = 3.0 # Ajusta esto para más o menos velocidad

# Bucle principal de control
while robot.step(timestep) != -1:
    # Lee los valores de los sensores
    # Un valor alto significa que el sensor está sobre una superficie oscura (la línea)
    # Un valor bajo significa que está sobre una superficie clara (el suelo blanco)
    ps0_value = ps0.getValue()
    ps7_value = ps7.getValue()

    # Define las velocidades de las ruedas
    left_speed = base_speed
    right_speed = base_speed

    # Lógica de seguimiento de línea simple
    # Si el sensor derecho (ps0) detecta la línea, gira a la izquierda
    if ps0_value > 500: # Umbral, ajusta según el contraste de tu línea
        left_speed = base_speed * 0.5 # Reduce la velocidad de la rueda izquierda
        right_speed = base_speed * 1.5 # Aumenta la velocidad de la rueda derecha
    # Si el sensor izquierdo (ps7) detecta la línea, gira a la derecha
    elif ps7_value > 500: # Umbral, ajusta según el contraste de tu línea
        left_speed = base_speed * 1.5 # Aumenta la velocidad de la rueda izquierda
        right_speed = base_speed * 0.5 # Reduce la velocidad de la rueda derecha
    # Si ambos sensores están sobre la línea o fuera de ella, avanza recto
    else:
        left_speed = base_speed
        right_speed = base_speed

    # Aplica las velocidades a los motores
    left_motor.setVelocity(left_speed)
    right_motor.setVelocity(right_speed)