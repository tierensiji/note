#CPU的调度思路
一、interactive+hotplug+cpuset方案。
1、interactive的target_loads的合理的频率表是分担负载的核心，频率表可以定义三丛集的核心，写上完整负荷频率对应表。
2、hotplug的各种阈值参数调配。
3、流畅度衡量的是系统均衡负载的能力，要求cpuset核心对于各类型的应用要设置合理。


二、接管联发科perfservices思路
***感谢https://en.miui.com/thread-2105983-1-1.html 各位大神提供的思路和代码！***
1、build.prop(ro.mtk_perfservice_support = 1 → ro.mtk_perfservice_support = 0)
2、 echo "0 2" > / proc / ppm / policy / userlimit_min_cpu_core
    echo "1 3" > / proc / ppm / policy / userlimit_min_cpu_core
    echo "2 1" > / proc / ppm / policy / userlimit_min_cpu_core
    -1（所有群集）
    值-1表示PPM将根据HPS（热插拔策略）设置自动调整它。
3、 echo "0 500000" > / proc / ppm / policy / userlimit_min_cpu_freq
    echo "1 800000" > / proc / ppm / policy / userlimit_min_cpu_freq
    echo "2 1400000" > / proc / ppm / policy / userlimit_min_cpu_freq
    -1（所有簇）
    值-1表示PPM将根据DVFS（动态电压和频率调整）设置自动调整。
4、 echo "7 0" > / proc / ppm / policy_status
    echo "8 1" > / proc / ppm / policy_status
    echo "9 1" > / proc / ppm / policy_status
    echo 3> / proc / cpufreq / cpufreq_power_mode
    echo "performance" > / proc / ppm / mode
    echo 0> / proc / ppm / policy / hica_is_limit_big_freq
    echo -1> / proc / ppm / policy / hica_power_state
    echo "2 2106000" / proc / ppm / policy / userlimit_max_cpu_freq
5、消除延迟（不提高性能）
su
echo 10000 > /dev/cpuctl/bg_non_interactive/cpu.rt_runtime_us
echo 950000 > /dev/cpuctl/cpu.rt_runtime_us
echo 1000000 > /dev/cpuctl/cpu.rt_period_us
echo 0 > / proc / sys / kernel / sched_tunable_scaling
echo 1 > / proc / mali / dvfs_enable
echo 1 > / proc / gpufreq / gpufreq_limited_thermal_ignore
echo 0 > / proc / ppm / policy / hica_is_limit_big_freq
6、消除延迟（耗电）
su
echo "7 0" > / proc / ppm / policy_status
echo "9 1" > / proc / ppm / policy_status
echo "0 3000 10 15 1 560 0 1000 0" > / proc / driver / thermal / clatm_setting
echo "1 2000 15 30 1 460 8000 700 0" > / proc / driver / thermal / clatm_setting
echo "4 60 50 40 30" > / proc / driver / thermal / clfps_level
echo 10000> /dev/cpuctl/bg_non_interactive/cpu.rt_runtime_us
echo 950000> /dev/cpuctl/cpu.rt_runtime_us
echo 1000000> /dev/cpuctl/cpu.rt_period_us
echo 0> / proc / sys / kernel / sched_tunable_scaling
echo 1> / proc / mali / dvfs_enable
echo 1> / proc / gpufreq / gpufreq_limited_thermal_ignore
echo 0> / proc / ppm / policy / hica_is_limit_big_freq
echo 2> / proc / ppm / policy / hica_power_state
echo 3> / proc / cpufreq / cpufreq_power_mode
echo "performance" > / proc / ppm / mode
echo "low-power-mode 0" > / d / ged / hal / event_notify
echo "low_power_mode：3" > / d / dispsys

三、perfserv+.tp方案
1、perfservapplist.txt鸡血阉割应用清单。
2、perfservscntbl.txt CPU场景控核策略。
3、.tp MTK温控编译。

#GPU的调度思路

#I/O的优化思路

#ZRAM的优化思路