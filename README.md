# project
import numpy as np

def generate_bpp_data(num_samples, max_container_size=(100, 100, 100), n_range=(10, 50)):
    """
    生成 BPP 数据集，包含容器和物品数据
    :param num_samples: 样本数量
    :param max_container_size: 容器最大尺寸
    :param n_range: 每个样本的物品数量范围
    :return: 数据集
    """
    dataset = []
    for _ in range(num_samples):
        container_size = tuple(np.random.randint(50, max_container_size[i] + 1) for i in range(3))
        items = [{"size": container_size, "position": (0, 0, 0), "rotation": (1, 1, 1)}]
        num_items = np.random.randint(*n_range)

        while len(items) < num_items:
            volumes = [np.prod(item["size"]) for item in items]
            probabilities = volumes / np.sum(volumes)
            selected_idx = np.random.choice(len(items), p=probabilities)
            selected_item = items.pop(selected_idx)

            axis = np.random.choice(3)
            size = selected_item["size"]
            position = selected_item["position"]

            split_point = np.random.uniform(0, size[axis])
            new_sizes = list(size)
            new_sizes[axis] = split_point
            remaining_size = size[axis] - split_point

            new_item1 = {
                "size": tuple(new_sizes),
                "position": position,
                "rotation": (1, 1, 1)
            }

            new_item2 = {
                "size": tuple([remaining_size if i == axis else size[i] for i in range(3)]),
                "position": tuple(
                    [position[i] if i != axis else position[i] + split_point for i in range(3)]
                ),
                "rotation": (1, 1, 1)
            }

            for item in [new_item1, new_item2]:
                rotation = np.random.choice([0, 90, 180, 270], size=3)
                item["rotation"] = rotation

            items.extend([new_item1, new_item2])

        dataset.append({"container_size": container_size, "items": items})
    return dataset

train_data = generate_bpp_data(10000)
test_data = generate_bpp_data(2000)
