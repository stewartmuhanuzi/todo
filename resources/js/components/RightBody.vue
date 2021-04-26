<template>
    <div id="right">
        <h2>Development CRM</h2>

        <div class="horizontal">
            <img src="/images/horizontal.png" alt="" />
        </div>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do
            eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim
            ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut
            aliquip ex ea commodo consequat. Duis aute irure dolor in
            reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla
            pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
            culpa qui officia deserunt mollit anim id est laborum.
        </p>

        <div class="task">
            <div class="add-tasks">
                <h2>Today's Task</h2>

                <div class="add-action">
                    <img src="/images/add.png" alt="" />
                </div>
            </div>

            <ul class="tasks-list"></ul>
        </div>

        <div class="upcoming">
            <div class="add-tasks">
                <h2>Upcoming</h2>

                <div class="add-action">
                    <img src="/images/add.png" alt="" />
                </div>
            </div>
            <form action="" @submit="addUpcomingTask">
                <input type="text" name="" v-model="newTask" />
            </form>
            <ul class="tasks-list">
                <li
                    v-for="upcomingtask in upcoming"
                    v-bind:key="upcomingtask.id"
                >
                    <div class="info">
                        <div class="left">
                            <label class="myCheckbox">
                                <input
                                    type="checkbox"
                                    name="test"
                                    :checked="upcomingtask.completed"
                                    @change="checkUpcoming(upcomingtask.taskId)"
                                />
                                <span></span>
                            </label>

                            <h4>{{ upcomingtask.title }}</h4>
                        </div>
                        <div class="right">
                            <img src="/images/edit.png" alt="" />
                            <img
                                src="/images/del.png"
                                alt=""
                                @click="delUpcoming(upcomingtask.taskId)"
                            />
                        </div>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            todayTask: [],
            upcoming: [],
            newTask: "",
        };
    },

    created() {
        this.fetchTodayTasks();
        this.fetchUpcoming();
    },

    methods: {
        //Upcoming Method Task

        //Get upcoming tasks
        fetchUpcoming() {
            fetch("/api/upcoming")
                .then((res) => res.json())
                .then(({ data }) => {
                    this.upcoming = data;
                })
                .catch((err) => console.log(err));
        },
        //Add Upcoming tasks
        addUpcomingTask(e) {
            e.preventDefault();
            if (this.upcoming.length > 4) {
                alert("Please complete the upcoming task");
            } else {
                const newTask = {
                    title: this.newTask,
                    waiting: true,
                    taskId: Math.random()
                        .toString(36)
                        .substring(7),
                };

                //Post Request
                fetch("api/upcoming", {
                    method: "POST",
                    headers: {
                        "content-type": "application/json",
                    },

                    body: JSON.stringify(newTask),
                }).then(() => this.upcoming.push(newTask));

                //Clear or reset
                this.newTask = "";
            }
        },

        //Delete Upcoming
        delUpcoming(taskId) {
            if (confirm("Are you sure?")) {
                fetch(`/api/upcoming/${taskId}`, {
                    method: "delete",
                })
                    .then((res) => res.json())
                    .then(() => {
                        this.upcoming = this.upcoming.filter(
                            ({ taskId: id }) => id !== taskId
                        );
                    })
                    .catch((err) => console.log(err));
            }
        },

        //Check Upcoming Task
        checkUpcoming(taskId) {
            if (this.todayTask.length > 4) {
                alert("Sorry complete exisitng task");
                window.location.href = "/";
            } else {
                this.addDailyTask(taskId);

                //Delete from this task from db
                fetch(`/api/upcoming/${taskId}`, { method: "delete" }).then(
                    () => {
                        this.upcoming = this.upcoming.filter(
                            ({ taskId: id }) => id !== taskId
                        );
                    }
                );
            }
        },

        //TodayTask Method
        //Get Today's Task
        fetchTodayTasks() {},

        addDailyTask(taskId){
            //Get task
            const task = this.upcoming.filter(({taskId:id})=>id==taskId)[0];
            //Post Task
            fetch("api/daily", {
                    method: "POST",
                    headers: {
                        "content-type": "application/json",
                    },

                    body: JSON.stringify(task),
                }).then(() => this.todayTask.unshift(task)).catch(err=>console.log(err));
        },
    },
};
</script>

<style></style>
