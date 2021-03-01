<template>
    <div>
        <b-nav tabs>
            <b-nav-item disabled><b>A Simple Quiz About Computers!</b></b-nav-item>
            <b-nav-item disabled>Counter: {{ numCorrect }}/{{ numTotal }}</b-nav-item>
        </b-nav>
    </div>
</template>

<script>
export default {
    props: {
        numCorrect: Number,
        numTotal: Number
    }
}
</script>