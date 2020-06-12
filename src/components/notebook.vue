<template>
    <div class="Mob">
        <div>
            <p>Имя: </p>
            <input type="text" placeholder="" v-model="params.name"/>
        </div>
        <div>
            <p>Фамилия: </p>
            <input type="text" placeholder="" v-model="params.surname"/>
        </div>
        <div>
            <p>Отчество: </p>
            <input type="text" placeholder="" v-model="params.patronymic"/>
        </div>
        <div>
            <p>Номер: </p>
            <input type="text" placeholder="+7(   ) _ _ _" v-model="params.phone"/>
        </div>
        <div class="box">
            <p>Избранное </p>
            <input class="box" type="checkbox" v-model="params.favorite"/>
        </div>
        <div>
            <p>Сортировка</p>
           по Имени<input v-model="sort" value="n_a" type="radio"  v-on:click ="onSort('n_a')">
           по Фамилии<input v-model="sort" value="n_b" type="radio" v-on:click ="onSort('n_b')">
        </div>
        <div>
            <p>Добавление</p>
           <button v-on:click="onAdd()">Добавить</button>
        </div>
        <div>
            <li v-for="(item, index) in posts" v-bind:key=index>
                    {{item.secondName}} {{item.firstName}} {{item.patronymic}} {{item.phone}}
                    <span v-if="item.favorite"> + </span>
                    <button v-on:click="onChange(index)" id="button-change">Изменить</button>
                    <button v-on:click="onDelete(index)" id="button-delete">Удалить</button>
                </li>
        </div>

        <!-- <button v-on:click="onLvUp">Повысить</button> -->
    </div>
</template>

<script>
export default {
        name: "posts",
        data() {
            return {
                params: {
                name: "",
                surname: "",
                patronymic: "",
                phone:"",
                favorite: false
            },
                sort: "n_a",
                posts:[],
                flag: -1,
                a_id: -1
            }
        },
        methods: {
            updateData() {
                try {
                    this.$http.get('http://localhost:3000/posts').then((res) => res.json()).then((res) => (this.posts = res));
                } catch (err) {
                    console.error(err);
                }
            },
            onSort (param) {
                if(param === "n_a") {
                    this.posts.sort((a, b) => a.name < b.name ? 1 : -1);
                }
                else if (param === "n_b") {
                    this.posts.sort((a, b) => a.surname < b.surname ? 1 : -1);
                }
                this.posts.sort((a, b) => a.favorite < b.favorite ? 1 : -1);
            },
            async onAdd() {
                    if (this.flag === -1) {
                        this.posts.push({
                            name: this.params.name,
                            surname: this.params.surname,
                            patronymic: this.params.patronymic,
                            phone: this.params.phone,
                            favorite: this.params.favorite
                        });
                        try{
                            await this.$http.post('http://localhost:3000/posts', this.params);
                            this.updateData();
                        }catch(err){
                            console.log(err);
                        }
                    }
                    else {
                        this.posts[this.flag] = {
                            name: this.params.name,
                            surname: this.params.surname,
                            patronymic: this.params.patronymic,
                            phone: this.params.phone,
                            favorite: this.params.favorite
                        };
                        try{
                            console.log(this.posts[this.flag]);
                            await this.$http.put('http://localhost:3000/posts/'+ this.a_id, this.posts[this.flag]);
                            this.updateData();
                        }catch(err){
                            console.log(err);
                        }
                            this.flag = -1;
                    }
                    if(this.sort === "n_a") {
                        this.posts.sort((a, b) => a.name < b.name ? 1 : -1);
                    }
                    else if (this.sort === "n_b") {
                        this.posts.sort((a, b) => a.surname < b.surname ? 1 : -1);
                    }
                    this.posts.sort((a, b) => a.favorite < b.favorite ? 1 : -1);
                    this.params.name = "";
                    this.params.surname = "";
                    this.params.patronymic = "";
                    this.params.phone = "";
                    this.params.favorite = "";
            },
            onChange (id) {
                this.params = {
                    name: this.posts[id].name,
                    surname: this.posts[id].surname,
                    patronymic: this.posts[id].patronymic,
                    phone: this.posts[id].phone,
                    favorite: this.posts[id].favorite
                };
                this.a_id = this.posts[id].id;
                this.flag = id;
            },
            async onDelete (id) {
                await this.$http.delete('http://localhost:3000/posts/' + this.posts[id].id);
                this.updateData();
            },
        },
        created() {
            this.onSort(this.sort);
            this.updateData();
        }
}
</script>