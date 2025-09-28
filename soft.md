## html to xml ##
tidy

## xml to html ...##
xsltproc
import bpy
import random

# -------- 清空场景 --------
bpy.ops.wm.read_factory_settings(use_empty=True)

# -------- 创建房间 --------
def create_room():
    # 地板
    bpy.ops.mesh.primitive_plane_add(size=10, location=(0,0,0))
    floor = bpy.context.active_object
    floor.name = "Floor"

    # 四面墙
    wall_height = 3
    wall_thickness = 0.1
    wall_length = 10

    walls = []
    positions = [ (0, -wall_length/2, wall_height/2),
                  (0, wall_length/2, wall_height/2),
                  (-wall_length/2, 0, wall_height/2),
                  (wall_length/2, 0, wall_height/2)]
    rotations = [ (0,0,0), (0,0,0), (0,0,1.5708), (0,0,1.5708) ]

    for i in range(4):
        bpy.ops.mesh.primitive_cube_add(size=1, location=positions[i])
        wall = bpy.context.active_object
        wall.scale = (wall_length/2, wall_thickness/2, wall_height/2)
        wall.rotation_euler = rotations[i]
        wall.name = f"Wall_{i+1}"
        walls.append(wall)

create_room()

# -------- 添加椅子 --------
def add_chair(location):
    bpy.ops.mesh.primitive_cube_add(size=1, location=location)
    chair = bpy.context.active_object
    chair.scale = (0.5,0.5,0.5)
    chair.name = "Chair"

    # 随机材质
    mat = bpy.data.materials.new(name=f"ChairMat_{random.randint(0,1000)}")
    mat.diffuse_color = (random.random(), random.random(), random.random(), 1)
    chair.data.materials.append(mat)

# 放置3张椅子
chair_positions = [(-2, -2, 0.25), (2, -2, 0.25), (0, 2, 0.25)]
for pos in chair_positions:
    add_chair(pos)

# -------- 添加摄像机 --------
bpy.ops.object.camera_add(location=(5, -5, 5), rotation=(1.1,0,0.785))
cam = bpy.context.active_object
bpy.context.scene.camera = cam

# -------- 添加光源 --------
bpy.ops.object.light_add(type='SUN', location=(5,5,10))
light = bpy.context.active_object
light.data.energy = 3

# -------- 保存场景 --------
bpy.ops.wm.save_as_mainfile(filepath="room_scene.blend")

print("Blender 场景生成完成！")