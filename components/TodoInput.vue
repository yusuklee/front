<script>
export default{
    date(){
        return{
            inputMsg:''
        }
    },
    emits:["addTodo"],
    methods:{
        addTodo(){ // 인풋에 입력한거를 부모한테 보내는 메서드라 보면됨
            
        this.$emit('addTodo',this.inputMsg);
        this.inputMsg="";
        
        }
    }
}
</script>
<template>
    <div class="todo__input">
        <input 
        type="text" 
        class="todo__input-text" 
        placeholder="할 일을 입력하세요"
        v-model="inputMsg"
        @keydown.enter="addTodo" />
        <button class ="todo__input-btn" @click="addTodo">등록</button>
    </div>
</template>