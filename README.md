# 
class Weapon:
    def __init__(self, name, damage, range):
        self.name = name
        self.damage = damage
        self.range = range

    def hit(self, actor, target):
        if not target.is_alive():
            print('Враг уже повержен')
        else:
            x1, y1 = actor
            x2, y2 = target.get_coords()
            dist = (abs(x1 - x2) ** 2 + abs(y1 - y2) ** 2) ** 0.5
            if self.range <= dist:
                print(f'Враг слишком далеко для оружия {self.name}')
            else:
                print(f'Врагу нанесен урон оружием {self.name} в размере {self.damage}')
                target.get_damage(self.damage)

    def __str__(self):
        return self.name


class BaseCharacter:
    def __init__(self, pos_x, pos_y, hp):
        self.pos_x = pos_x
        self.pos_y = pos_y
        self.hp = hp

    def move(self, delta_x, delta_y):
        self.pos_x += delta_x
        self.pos_y += delta_y

    def is_alive(self):
        if self.hp > 0:
            return True
        return False

    def get_damage(self, amount):
        self.hp -= amount

    def get_coords(self):
        return self.pos_x, self.pos_y


class BaseEnemy(BaseCharacter):
    def __init__(self, pos_x, pos_y, weapon, hp):
        super().__init__(pos_x, pos_y, hp)
        self.weapon = weapon

    def hit(self, target):
        if not isinstance(target, MainHero):
            print('Могу ударить только Главного героя')
        else:
            self.weapon.hit(actor=(self.pos_x, self.pos_x), target=target)

    def __str__(self):
        return f'Враг на позиции {self.pos_x, self.pos_y} с оружием {self.weapon}'


class MainHero(BaseCharacter):
    def __init__(self, pos_x, pos_y, name, hp):
        super().__init__(pos_x, pos_y, hp)
        self.name = name
        self.weapon_list = []
        self.id_weapon = 0

    def hit(self, target):
        if len(self.weapon_list) == 0:
            print('Я безоружен')
        else:
            if isinstance(target, BaseEnemy):
                self.weapon_list[self.id_weapon - 1].hit(actor=(self.pos_x, self.pos_y), target=target)
            else:
                print('Могу ударить только Врага')

    def add_weapon(self, weapon):
        if isinstance(weapon, Weapon):
            if len(self.weapon_list) == 0:
                self.id_weapon += 1
            self.weapon_list.append(weapon)
            print(f'Подобрал {weapon}')
        else:
            print('Это не оружие')

    def next_weapon(self):
        if len(self.weapon_list) == 0:
            print('Я безоружен')
        elif len(self.weapon_list) == 1:
            print('У меня только одно оружие')
        else:
            if len(self.weapon_list) < self.id_weapon:
                self.id_weapon = 0
            else:
                self.id_weapon += 1
            print(f'Сменил оружие на {self.weapon_list[self.id_weapon - 1]}')

    def heal(self, amount):
        if self.hp + amount >= 200:
            self.hp = 200
        else:
            self.hp += amount
        print(f'Полечился, теперь здоровья {self.hp}')


