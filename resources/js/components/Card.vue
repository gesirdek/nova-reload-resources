<template>
    <card class="flex flex-col items-center justify-center p-1">
        <div class="mb-1">
            <button class="text-center btn btn-primary btn-default" @click="reloadResources">{{ __('Reload Resources') }}</button>
            <br />
            <input type="checkbox" id="checkbox" v-model="checked" @change="setTimer(checked)">
            <label for="checkbox">{{ __('Load every 10 seconds') }}</label>
        </div>
    </card>
</template>

<script>
export default {
    props: [
        'card',
    ],
    data: () => ({
        'resourceName':null,
        'timer':false
    }),
    mounted() {
        this.resourceName = this.$router.currentRoute.params.resourceName;
    },
    methods:{
        setTimer(checked){
            this.timer = checked;
            if(checked)
                setTimeout(this.callAutoReload, 10000);
        },
        async reloadResources(){
            if(this.resourceName){
                let filters_backup = _.cloneDeep(this.$store.getters[`${this.resourceName}/filters`]);
                let filters_to_change = _.cloneDeep(filters_backup);
                filters_to_change.push({});
                await this.$store.commit(`${this.resourceName}/storeFilters`,filters_to_change);
                await this.$store.commit(`${this.resourceName}/storeFilters`,filters_backup);       
            }
        },
        callAutoReload(){
            if(this.timer === true){
                this.reloadResources().then(
                    setTimeout(this.callAutoReload, 10000)
                );
            }
        }
    }
}
</script>
