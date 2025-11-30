#include <stdio.h>

struct Process {
    int pid;
    int burst_time;
    int remaining_time;
    int arrival_time;
};

void round_robin_scheduler(struct Process processes[], int num_processes, int time_quantum) {
    int current_time = 0;
    int waiting_time = 0;
    int queue[num_processes];
    int front = 0, rear = 0;
	int i;
    for (i = 0; i < num_processes; i++) {
        processes[i].remaining_time = processes[i].burst_time;
    }

    while (1) {
        for (i = 0; i < num_processes; i++) {
            if (processes[i].arrival_time <= current_time && processes[i].remaining_time > 0) {
                if (processes[i].remaining_time <= time_quantum) {
                    current_time += processes[i].remaining_time;
                    waiting_time += current_time - processes[i].arrival_time - processes[i].burst_time;
                    processes[i].remaining_time = 0;
                } else {
                    current_time += time_quantum;
                    processes[i].remaining_time -= time_quantum;
                }
                queue[rear++] = i;
            }
        }

        if (front == rear)
            break;

        int next_process = queue[front++];
        if (processes[next_process].remaining_time > 0) {
            queue[rear++] = next_process;
        }
    }

    printf("Total waiting time for processes: %d milliseconds\n", waiting_time);
}

int main() {
    int num_processes, time_quantum,i;

    printf("Enter the number of processes: ");
    scanf("%d", &num_processes);

    struct Process processes[num_processes];
    for (i = 0; i < num_processes; i++) {
        printf("Enter burst time for process %d: ", i + 1);
        scanf("%d", &processes[i].burst_time);
        printf("Enter arrival time for process %d: ", i + 1);
        scanf("%d", &processes[i].arrival_time);
        processes[i].pid = i + 1;
    }

    printf("Enter the time quantum: ");
    scanf("%d", &time_quantum);

    round_robin_scheduler(processes, num_processes, time_quantum);

    return 0;
}
