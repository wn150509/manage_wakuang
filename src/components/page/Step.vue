<template>
    <div class="step">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-document"></i> 步骤条</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <el-steps :active="1">
                <el-step title="步骤 1" description="这是一段很长很长很长的描述性文字"></el-step>
                <el-step title="步骤 2" description="这是一段很长很长很长的描述性文字"></el-step>
                <el-step title="步骤 3" description="这段就没那么长了"></el-step>
            </el-steps>
        </div>
        <div class="container">
            <el-steps :active="1">
                <el-step title="步骤 1" icon="el-icon-edit"></el-step>
                <el-step title="步骤 2" icon="el-icon-upload"></el-step>
                <el-step title="步骤 3" icon="el-icon-picture"></el-step>
            </el-steps>
        </div>
        <div class="container">
            <el-steps :active="1" finish-status="success" simple style="margin-top: 20px">
                <el-step title="步骤 1"></el-step>
                <el-step title="步骤 2"></el-step>
                <el-step title="步骤 3"></el-step>
            </el-steps>
        </div>
    </div>
    </div>
</template>

<script>
    export default {
        name: "Step.vue"
    }
</script>

<style scoped>
    .white {
        background: #FFF;
    }
</style>
